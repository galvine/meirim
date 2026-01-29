# תכנית מיגרציה: מאירים Node.js → Django

> מסמך מאסטר למעקב התקדמות וחידוש שיחות

## סטטוס נוכחי

| שלב | סטטוס | תאריך עדכון |
|-----|--------|-------------|
| 0. תכנון ותשתית | 🔄 בתהליך | 2026-01-29 |
| 1. חילוץ DB Schema | ⏳ ממתין | - |
| 2. חילוץ API Spec | ⏳ ממתין | - |
| 3. חילוץ Business Logic | ⏳ ממתין | - |
| 4. חילוץ Crawlers | ⏳ ממתין | - |
| 5. חילוץ Emails & Workflows | ⏳ ממתין | - |
| 6. Setup Django Project | ⏳ ממתין | - |
| 7. בניית Models | ⏳ ממתין | - |
| 8. בניית API | ⏳ ממתין | - |
| 9. בניית Crawlers | ⏳ ממתין | - |
| 10. Frontend Decision | ⏳ ממתין | - |

**סימונים:** ✅ הושלם | 🔄 בתהליך | ⏳ ממתין | ❌ חסום

---

## מבנה התיעוד

```
django-migration/
├── PLAN.md                    # מסמך זה - מפת דרכים ראשית
├── CONTEXT.md                 # סיכום להעברה בין שיחות
├── docs/
│   ├── 01-project-overview.md # סקירת הפרויקט המקורי
│   ├── 02-database-schema.md  # ERD ומודלים
│   ├── 03-api-specification.md # OpenAPI spec
│   ├── 04-business-logic.md   # חוקים עסקיים
│   ├── 05-crawlers.md         # לוגיקת סריקה
│   ├── 06-emails-workflows.md # תבניות ותהליכים
│   └── 07-decisions.md        # החלטות ארכיטקטורה
├── specs/
│   ├── openapi.yaml           # API spec בפורמט OpenAPI
│   ├── erd.mermaid            # דיאגרמת DB
│   └── models.py              # Django models (draft)
└── progress/
    └── session-log.md         # לוג שיחות ופעולות
```

---

## שלב 0: תכנון ותשתית

### משימות
- [x] סקירת הפרויקט המקורי
- [x] הגדרת מבנה תיעוד
- [ ] יצירת קובץ CONTEXT.md
- [ ] יצירת קובץ session-log.md
- [ ] החלטה על מיקום הרפו החדש

### החלטות נדרשות
1. **מיקום רפו Django:**
   - אפשרות א: תיקייה `django/` בתוך מאירים הקיים
   - אפשרות ב: רפו נפרד לגמרי
   - אפשרות ג: fork של מאירים והחלפה הדרגתית

2. **שם הפרויקט החדש:** מאירים? שם חדש?

3. **Frontend:**
   - להשאיר React קיים?
   - Django templates + HTMX?
   - לבנות מחדש?

---

## שלב 1: חילוץ DB Schema

### מטרה
ליצור ERD מלא ותיעוד של כל הטבלאות, שדות, ויחסים.

### קבצי מקור לניתוח
```
server/api/model/*.js          # 35 מודלים
server/migrations/*.js         # 18+ migrations
server/knexfile.js            # DB config
```

### פלט צפוי
- `docs/02-database-schema.md` - תיעוד מילולי
- `specs/erd.mermaid` - דיאגרמה ויזואלית
- `specs/models.py` - Django models ראשוניים

### מודלים עיקריים לתיעוד
| מודל | קובץ מקור | עדיפות |
|------|-----------|--------|
| Plan | plan.js (~18KB) | קריטי |
| Person | person.js (~6.5KB) | קריטי |
| Alert | alert.js (~5.4KB) | קריטי |
| TreePermit | tree_permit.js | גבוה |
| Comment | comment.js | גבוה |
| Notification | notification.js | בינוני |
| ... | ... | ... |

---

## שלב 2: חילוץ API Spec

### מטרה
ליצור OpenAPI specification מלא של כל ה-endpoints.

### קבצי מקור לניתוח
```
server/api/apiRoutes.js        # כל ה-routes
server/api/controller/*.js     # 27 controllers
```

### פלט צפוי
- `docs/03-api-specification.md` - תיעוד מילולי
- `specs/openapi.yaml` - OpenAPI 3.0 spec

### Endpoints לתיעוד
- Auth (sign up, sign in, password reset)
- Plans (CRUD, subscribe, search)
- Alerts (CRUD)
- Trees (list, details, geojson)
- Comments (CRUD, like)
- Permits (list, AOI)
- Public API

---

## שלב 3: חילוץ Business Logic

### מטרה
לתעד את כל הלוגיקה העסקית, validations, ו-edge cases.

### קבצי מקור לניתוח
```
server/api/controller/*.js     # לוגיקה עיקרית
server/api/lib/*.js           # utility functions
server/api/lib/tags/*.js      # תיוג אוטומטי
```

### פלט צפוי
- `docs/04-business-logic.md`

### נושאים לתיעוד
- תהליך הרשמה והפעלת משתמש
- לוגיקת התאמת תכניות להתראות (גיאוגרפיה)
- תיוג אוטומטי של תכניות
- חישוב סטטיסטיקות
- Rate limiting / spam protection

---

## שלב 4: חילוץ Crawlers

### מטרה
לתעד את כל הסורקים - מקורות, לוגיקת parsing, scheduling.

### קבצי מקור לניתוח
```
server/api/lib/iplanApi.js     # סורק ראשי
server/api/lib/mavat/          # סורק מבא"ת
server/api/lib/trees/*.js      # 13 סורקי עצים
server/bin/*                   # entry points
```

### פלט צפוי
- `docs/05-crawlers.md`

### סורקים לתיעוד
| סורק | מקור | תדירות | עדיפות |
|------|------|---------|--------|
| iPlan | Kavim Kchulim API | 40 דק' | קריטי |
| Mavat | מבא"ת (scraping) | on-demand | קריטי |
| TLV Trees | עיריית ת"א | יומי | גבוה |
| Jerusalem Trees | עיריית י-ם | יומי | גבוה |
| ... | ... | ... | ... |

---

## שלב 5: חילוץ Emails & Workflows

### מטרה
לתעד תבניות אימייל, טריגרים, ותהליכים אוטומטיים.

### קבצי מקור לניתוח
```
server/api/view/email/*.mustache  # 11 תבניות
server/api/service/email.js       # לוגיקת שליחה
server/bin/send_emails*           # jobs
```

### פלט צפוי
- `docs/06-emails-workflows.md`

---

## שלב 6-10: בניית Django

(יפורט לאחר השלמת שלבי החילוץ)

---

## כללים לעבודה

### חידוש שיחה
בתחילת כל שיחה חדשה:
1. לקרוא `CONTEXT.md` - סיכום מצב נוכחי
2. לקרוא `progress/session-log.md` - מה נעשה
3. להמשיך מהנקודה האחרונה

### סיום שיחה
בסוף כל שיחה:
1. לעדכן `CONTEXT.md` עם הסטטוס הנוכחי
2. להוסיף לוג ל-`session-log.md`
3. לעדכן סטטוסים ב-`PLAN.md`

### עקרונות תיעוד
- כל קובץ תיעוד עומד בפני עצמו
- לכלול דוגמאות קוד מהמקור
- לציין החלטות ו-trade-offs
- לסמן שאלות פתוחות ב-`[?]`

---

## שאלות פתוחות

1. [ ] האם ליצור רפו חדש או להישאר כאן?
2. [ ] מה שם הפרויקט החדש?
3. [ ] מה עושים עם ה-Frontend?
4. [ ] האם לשמור תאימות API מלאה?
5. [ ] האם להוסיף פיצ'רים חדשים כבר עכשיו?

---

## קישורים שימושיים

- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [GeoDjango](https://docs.djangoproject.com/en/4.2/ref/contrib/gis/)
- [Celery](https://docs.celeryq.dev/)
