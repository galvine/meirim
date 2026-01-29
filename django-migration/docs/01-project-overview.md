# סקירת פרויקט מאירים - מקור להמרה

> תיעוד מלא של הפרויקט המקורי לצורך חילוץ אפיון

---

## מטרת הפרויקט

**מאירים** היא פלטפורמת טכנולוגיה אזרחית (Civic Tech) שמטרתה:
- להעצים אזרחים להתארגן למען איכות החיים בערים שלהם
- להנגיש מידע על תכניות בנייה והתנגדויות
- לאפשר לאזרחים לקבל התראות על תכניות חדשות באזורים רלוונטיים

---

## ארכיטקטורה

### תרשים כללי
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Kavim Kchulim │────▶│    Crawler      │────▶│     MySQL       │
│   + Mavat APIs  │     │   (Puppeteer)   │     │    Database     │
└─────────────────┘     └─────────────────┘     └────────┬────────┘
                                                         │
                        ┌─────────────────┐              │
                        │   Express API   │◀─────────────┘
                        │   (port 3001)   │
                        └────────┬────────┘
                                 │
                        ┌────────▼────────┐
                        │   React App     │
                        │   (port 3000)   │
                        └─────────────────┘
```

### שלושת הרכיבים העיקריים

| רכיב | תיאור | טכנולוגיות | מיקום |
|------|-------|------------|-------|
| **Backend** | API ושרת | Node.js 18, Express, Bookshelf ORM | `/server` |
| **Frontend** | אתר האינטרנט | React 17, Material-UI 4, Redux | `/client` |
| **Crawler** | סורק תכניות | Puppeteer, Cheerio | `/server/api/lib/` |

---

## Backend - מבנה מפורט

### Entry Points (`/server/bin/`)
| סקריפט | תיאור |
|--------|-------|
| `api` | שרת פיתוח (port 3001) |
| `serve` | שרת production (port 80) |
| `iplan` | סריקת תכניות |
| `send_emails` | שליחת התראות תכניות |
| `send_emails_trees` | שליחת התראות עצים |
| `fetch_tree_permit` | סריקת היתרי עצים |
| `aggregate_views` | צבירת צפיות |
| `plan_status_change` | מעקב שינויי סטטוס |
| `send_digest_emails` | שליחת digest |

### Controllers (`/server/api/controller/`) - 27 קבצים
| Controller | תיאור | גודל |
|------------|-------|------|
| `plan.js` | תכניות בנייה | - |
| `sign.js` | הרשמה/התחברות | - |
| `alert.js` | התראות | - |
| `comment.js` | תגובות | - |
| `tree_permit.js` | היתרי עצים | - |
| `funding.js` | תרומות | - |
| `rate.js` | דירוגים | - |
| `impression.js` | צפיות | - |
| `cron.js` | לוגיקת jobs | ~18KB |
| `permit.js` | היתרי בנייה | - |
| `permit_aoi.js` | אזורי עניין להיתרים | - |

### Models (`/server/api/model/`) - 35 קבצים
| Model | תיאור | גודל |
|-------|-------|------|
| `plan.js` | תכניות | ~18KB |
| `person.js` | משתמשים | ~6.5KB |
| `alert.js` | התראות | ~5.4KB |
| `tree_permit.js` | היתרי עצים | ~2.7KB |
| `comment.js` | תגובות | - |
| `rate.js` | דירוגים | - |
| `notification.js` | התראות מערכת | - |
| `funding_transaction.js` | עסקאות תרומה | - |
| `plan_chart_*.js` | נתוני גרפים | - |
| `permit_aoi.js` | אזורי עניין | - |
| `activity.js` | פעילות | - |

### Services (`/server/api/service/`)
| Service | תיאור | גודל |
|---------|-------|------|
| `database.js` | חיבור DB | - |
| `email.js` | שליחת מיילים | ~7.2KB |
| `digest_email.js` | digest מיילים | - |
| `geocoder.js` | גיאוקודינג | - |
| `staticmap.js` | מפות סטטיות | - |

### Libraries (`/server/api/lib/`)

#### סורק ראשי
| קובץ | תיאור | גודל |
|------|-------|------|
| `iplanApi.js` | API קווים כחולים | - |
| `mavat/index.js` | סורק מבא"ת | ~13KB |

#### סורקי עצים (`/server/api/lib/trees/`) - 13 קבצים
| קובץ | עיר/מקור |
|------|----------|
| `tlv_tree_permit.js` | תל אביב |
| `jerusalem_tree_permit.js` | ירושלים |
| `haifa_tree_permit.js` | חיפה |
| `ramat_gan_tree_permit.js` | רמת גן |
| `beer_sheva_tree_permit.js` | באר שבע |
| `hod_hasharon_tree_permit.js` | הוד השרון |
| `yavne_tree_permit.js` | יבנה |
| `kkl_tree_permit.js` | קק"ל |
| `regional_tree_permit.js` | מועצות אזוריות |
| `tree_crawler.js` | לוגיקה משותפת |
| `tree_crawler_excel.js` | קריאת Excel |

#### מערכת תגיות (`/server/api/lib/tags/`)
| קובץ | קטגוריה |
|------|----------|
| `commerce.js` | מסחר |
| `employment.js` | תעסוקה |
| `housing.js` | דיור |
| `forests.js` | יערות |
| `light_rail.js` | רכבת קלה |
| `streams.js` | נחלים |
| `public.js` | מבני ציבור |
| `hoteliery.js` | מלונאות |

### Email Templates (`/server/api/view/email/`) - 11 תבניות
| תבנית | שימוש |
|--------|-------|
| `alert.mustache` | התראת תכנית |
| `treeAlert.mustache` | התראת עצים |
| `newAlert.mustache` | אישור התראה חדשה |
| `resetPasswordToken.mustache` | איפוס סיסמה |
| `newSignUp.mustache` | הרשמה חדשה |
| `planDeposit.mustache` | הפקדת תכנית |
| `wrapper.mustache` | עטיפת מייל |

---

## Frontend - מבנה מפורט

### Pages (`/client/src/pages/`) - 10 דפים
| דף | תיאור |
|----|-------|
| `Homepage` | דף הבית |
| `Plan` | פרטי תכנית + חיפוש |
| `Tree` | היתרי עצים |
| `TreeMap` | מפת עצים |
| `Permits` | היתרי בנייה |
| `Funding` | תרומות |
| `Login` | התחברות |
| `Register` | הרשמה |
| `UrbanPlanning` | מידע תכנון |
| `UserPlans` | התכניות שלי |

### State Management (Redux)
| Slice | תיאור |
|-------|-------|
| `plan/` | מצב תכניות |
| `search/` | חיפוש וסינון |
| `tree/` | היתרי עצים |
| `comments/` | תגובות |
| `funding/` | תרומות |
| `modal/` | מודלים |
| `user/` | משתמש |

### Components (`/client/src/components/`)
- AlertList, AlertPlans, AlertTrees
- Comments
- FilterBox, AutoComplete
- BlockParcelAutocomplete
- Links, Footer, InfoBadge

### Shared Components (`/client/src/shared/`)
- row, text, icon, divider
- input, textarea, label
- dropdown, modal, menu
- tabBox, tabPanel
- progressBar, planCard

---

## API Endpoints

### Authentication
```
POST /sign/up              # הרשמה
POST /sign/activate        # הפעלת חשבון
POST /sign/in              # התחברות
POST /sign/out             # התנתקות
POST /password/sendResetToken
POST /password/resetWithToken
```

### Plans
```
GET  /plan/                # רשימת תכניות
GET  /plan/:id             # פרטי תכנית
GET  /plan/user            # תכניות המשתמש
GET  /plan_county          # תכניות לפי מחוז
GET  /plan_status          # סטטוסים
POST /plan/:id/subscribe   # הרשמה להתראות
DELETE /plan/:id/subscribe # ביטול הרשמה
```

### Trees
```
GET /tree/                 # רשימת היתרים
GET /tree/:id              # פרטי היתר
GET /trees/geojson/        # GeoJSON למפה
GET /tree_place            # מקומות
```

### Alerts
```
GET  /alert/               # התראות המשתמש
GET  /alert/:id            # פרטי התראה
POST /alert/               # יצירת התראה
DELETE /alert/:id          # מחיקת התראה
DELETE /alert/_token/:token # ביטול בטוקן
```

### Comments & Engagement
```
GET  /comment/:plan_id     # תגובות לתכנית
POST /comment/:plan_id     # הוספת תגובה
POST /comment/like/add     # לייק
GET  /rate/:plan_id        # דירוגים
POST /rate/                # דירוג
POST /impression/:plan_id  # צפייה
```

### Permits
```
GET  /permit/              # היתרי בנייה
GET  /permit/aoi           # אזורי עניין
GET  /permit/aoi/:id/preview
GET  /permit/aoi/person    # AOI של משתמש
POST /permit/aoi/person    # יצירת AOI
DELETE /permit/aoi/person/:id
```

### Other
```
GET  /funding/stats        # סטטיסטיקות תרומות
GET  /funding/paymentLink  # לינק לתשלום
POST /funding/             # תרומה
GET  /topfive              # 5 תכניות מובילות
GET  /centroid             # מרכז גיאוגרפי
GET  /health               # בדיקת בריאות
GET  /version              # גרסה
GET  /public/plan          # API ציבורי
```

---

## Cron Jobs

| Job | תדירות | תיאור |
|-----|---------|-------|
| `iplan` | */40 * * * * | סריקת תכניות |
| `plan_status_change` | */20 * * * * | מעקב שינויים |
| `fetch_tree_permit` | 0 10,21 * * SUN-THU | סריקת עצים |
| `send_emails` | */50 * * * * | התראות תכניות |
| `send_emails_trees` | */50 * * * * | התראות עצים |
| `aggregate_views` | 30 * * * * | צבירת צפיות |

---

## טכנולוגיות

### Backend Stack
- Node.js 18
- Express.js
- Bookshelf.js / Knex.js (ORM)
- MySQL 5.7
- Puppeteer (scraping)
- Nodemailer + SendGrid (email)
- OpenTelemetry + Coralogix (metrics)
- Sentry (errors)
- Winston (logging)
- AWS SDK (S3)

### Frontend Stack
- React 17
- Redux + Redux Toolkit
- Material-UI 4
- Styled Components
- Leaflet + Mapbox GL (maps)
- React Router 5
- Axios

### Infrastructure
- AWS ECS Fargate
- Amazon ECR
- Amazon S3
- GitHub Actions (CI/CD)
- Docker
- PM2

---

## בעיות/הערות לשיפור בפרויקט החדש

### API
- [ ] Inconsistent naming (`plan_county` vs `planStatus`)
- [ ] חסר pagination בחלק מה-endpoints
- [ ] אין API versioning
- [ ] מעורבב REST עם RPC-style

### קוד
- [ ] React 17 (לא 18)
- [ ] React Scripts 3.4 (לא 5.x)
- [ ] JavaScript (לא TypeScript)
- [ ] Crawler משולב ב-server (לא מופרד)

### תשתית
- [ ] כיסוי בדיקות לא מלא
- [ ] תלויות ישנות (eslint 6, mocha 8)

---

*נוצר: 2026-01-29 | מקור: ניתוח קוד מאירים*
