# 🔔 Notification System — System Design

> Design a system that can send millions of push, email, and SMS notifications reliably and at scale.

---

## 📋 Requirements

### Functional Requirements
- Support notification types: Push (mobile), Email, SMS, In-App
- Send notifications reliably (at-least-once delivery)
- Support scheduled notifications (e.g., send at 9am user's timezone)
- Support bulk notifications (broadcast to millions of users)
- Track delivery status (sent, delivered, failed, opened)

### Non-Functional Requirements
- **Scale**: 10 million notifications/day
- **High availability**: 99.9% uptime
- **Eventual consistency**: Delivery may be delayed, but must happen
- **Retry on failure**: If push fails, retry 3 times
- **Observability**: Track delivery rates and failures

---

## 📊 Capacity Estimation

```
10M notifications/day = ~115 notifications/second (avg)
Peak: 5x average = ~575/sec

Storage:
- Notification record: ~1KB
- 10M/day × 365 = 3.65B/year = ~3.65 TB/year
→ Store recent 30 days in hot storage, archive to S3
```

---

## 🏗️ High-Level Architecture

```
┌───────────────────────────────────────────────────────────┐
│                     Notification Service                   │
│                                                           │
│  API Server ─→ [Message Queue] ─→ Workers                │
│                (Kafka/SQS)        ├── Push Worker          │
│                                   │   (FCM / APNs)        │
│                                   ├── Email Worker         │
│                                   │   (SendGrid/SES)      │
│                                   └── SMS Worker           │
│                                       (Twilio/SNS)        │
└───────────────────────────────────────────────────────────┘
       ↑ Triggers notifications
External Services (Order, Payment, etc.)
```

---

## 🔑 Component Design

### 1. Notification Service API

```python
# POST /api/notifications/send
{
    "userId": "12345",
    "type": "order_confirmed",
    "channels": ["push", "email"],  # Which channels to use
    "priority": "high",             # high | normal | low
    "data": {
        "orderId": "ORD-789",
        "amount": 2500,
        "message": "Your order has been confirmed!"
    },
    "scheduleAt": null              # null = send immediately
}

# POST /api/notifications/broadcast
{
    "segment": "all_users",  # or specific user IDs
    "type": "promotional",
    "channels": ["push"],
    "data": {"message": "Flat 20% off today!"},
    "scheduleAt": "2026-06-10T09:00:00+05:30"
}
```

### 2. Message Queue (Kafka)

```
Topics:
- notifications.push.high_priority
- notifications.push.normal
- notifications.email
- notifications.sms
- notifications.status_updates

Partitioning:
- Push: Partition by user_id (preserve order per user)
- Email: Partition by domain (avoid rate limits)
- SMS: Partition by phone prefix (country-based routing)
```

### 3. User Preference Service

Before sending, check user preferences:

```python
class UserPreferences:
    def should_send(self, user_id, channel, notification_type):
        prefs = self.get_prefs(user_id)
        
        # Check opt-out
        if channel in prefs.opted_out_channels:
            return False
        
        # Check DND (Do Not Disturb) hours
        user_tz = prefs.timezone
        local_hour = get_local_hour(user_tz)
        if local_hour < 8 or local_hour > 22:  # DND: 10pm - 8am
            if notification_type != 'critical':
                return False
        
        # Check notification type preferences
        return prefs.enabled_types.get(notification_type, True)
```

### 4. Push Notification Worker

```python
import firebase_admin
from firebase_admin import messaging

class PushWorker:
    def send(self, notification):
        user_device = self.get_device_token(notification.user_id)
        
        if not user_device:
            self.log_failure(notification, "No device token")
            return
        
        message = messaging.Message(
            notification=messaging.Notification(
                title=notification.title,
                body=notification.body,
            ),
            data=notification.data,
            token=user_device.fcm_token,
        )
        
        try:
            response = messaging.send(message)
            self.update_status(notification.id, "delivered", response)
        except messaging.UnregisteredError:
            # Device token invalid — user uninstalled app
            self.deregister_device(user_device.id)
            self.update_status(notification.id, "failed", "unregistered")
        except Exception as e:
            self.retry(notification)

    def retry(self, notification, attempt=1, max_attempts=3):
        if attempt > max_attempts:
            self.update_status(notification.id, "failed_permanently")
            return
        
        delay = 2 ** attempt  # Exponential backoff: 2, 4, 8 seconds
        self.queue.send_delayed(notification, delay_seconds=delay, attempt=attempt+1)
```

### 5. Email Worker

```python
import sendgrid
from sendgrid.helpers.mail import Mail

class EmailWorker:
    def send(self, notification):
        user = self.get_user(notification.user_id)
        template = self.get_template(notification.type)
        
        message = Mail(
            from_email="noreply@gramsathi.in",
            to_emails=user.email,
            subject=template.render_subject(notification.data),
            html_content=template.render_body(notification.data)
        )
        
        try:
            sg = sendgrid.SendGridAPIClient(api_key=os.environ.get('SENDGRID_API_KEY'))
            response = sg.client.mail.send.post(request_body=message.get())
            self.update_status(notification.id, "sent")
        except Exception as e:
            self.retry(notification)
```

---

## 🗄️ Database Design

```sql
-- Notifications log
CREATE TABLE notifications (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id         INTEGER NOT NULL,
    type            VARCHAR(50) NOT NULL,
    channel         VARCHAR(20) NOT NULL,   -- push | email | sms | in_app
    status          VARCHAR(20) DEFAULT 'pending', -- pending | sent | delivered | failed
    title           VARCHAR(200),
    body            TEXT,
    data            JSONB,
    priority        VARCHAR(10) DEFAULT 'normal',
    retry_count     INTEGER DEFAULT 0,
    scheduled_at    TIMESTAMP,
    sent_at         TIMESTAMP,
    delivered_at    TIMESTAMP,
    created_at      TIMESTAMP DEFAULT NOW()
);

-- User preferences
CREATE TABLE notification_preferences (
    user_id             INTEGER PRIMARY KEY,
    push_enabled        BOOLEAN DEFAULT true,
    email_enabled       BOOLEAN DEFAULT true,
    sms_enabled         BOOLEAN DEFAULT true,
    dnd_start_hour      INTEGER DEFAULT 22,
    dnd_end_hour        INTEGER DEFAULT 8,
    timezone            VARCHAR(50) DEFAULT 'Asia/Kolkata',
    opted_out_types     TEXT[] DEFAULT '{}',
    updated_at          TIMESTAMP DEFAULT NOW()
);

-- Device tokens
CREATE TABLE device_tokens (
    id          SERIAL PRIMARY KEY,
    user_id     INTEGER NOT NULL,
    platform    VARCHAR(10),    -- android | ios | web
    fcm_token   VARCHAR(500),
    is_active   BOOLEAN DEFAULT true,
    updated_at  TIMESTAMP DEFAULT NOW()
);
```

---

## 🔑 Handling Scale: Broadcast to Millions

For broadcasting to all users:

```python
def broadcast_notification(message, filters):
    # Batch users in chunks of 1000
    BATCH_SIZE = 1000
    offset = 0
    
    while True:
        user_batch = db.query(
            "SELECT user_id FROM users WHERE active=true LIMIT %s OFFSET %s",
            BATCH_SIZE, offset
        )
        
        if not user_batch:
            break
        
        # Send batch to Kafka
        for user in user_batch:
            kafka_producer.send("notifications.push.normal", {
                "user_id": user.id,
                "type": message.type,
                "data": message.data
            })
        
        offset += BATCH_SIZE
    
    # Kafka consumers process in parallel
    # 10M users / 1000 batch = 10,000 batches → sent in minutes
```

---

## ❓ Interview Questions

**Q: How do you ensure at-least-once delivery?**
> Use a message queue (Kafka) with acknowledgment. Consumers only ACK after successfully sending. If a consumer crashes before ACK, Kafka redelivers to another consumer. On the delivery side, retry with exponential backoff.

**Q: How do you prevent sending duplicate notifications?**
> Use idempotency keys. When enqueueing, generate a `notification_id`. The worker checks DB before sending: if `status = sent/delivered`, skip. This prevents duplicates on retry.

**Q: How do you handle DND (Do Not Disturb)?**
> Store user's timezone and DND hours in preferences. Before sending, check current local time against DND window. For non-critical notifications, schedule to queue them and send when DND ends.

**Q: How would you design the delivery receipt system?**
> For push: FCM/APNs webhooks confirm delivery. For email: Sendgrid webhooks for open/click tracking (pixel tracking). For SMS: Carrier delivery receipts via Twilio webhooks. All events are stored in the notifications table and can power dashboards.

---

## ✅ Key Takeaways

- Use message queues (Kafka) for async, scalable notification delivery
- Separate workers for each channel (push, email, SMS)
- Implement retry with exponential backoff (max 3 attempts)
- Check user preferences before every send (opt-outs, DND)
- Track delivery status for observability and debugging
- For broadcasts, batch users and process in parallel workers
