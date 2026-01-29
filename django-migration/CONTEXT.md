# קונטקסט למיגרציה: מאירים → Django

> **קובץ זה הוא נקודת הכניסה לחידוש שיחות.**
> יש לקרוא אותו בתחילת כל שיחה חדשה.

---

## TL;DR - מה קורה כאן?

אנחנו ממירים את פרויקט **מאירים** (פלטפורמת Civic Tech לתכנון עירוני) מ-**Node.js/React** ל-**Django/Python**.

**הגישה:** חילוץ אפיון מפורט מהקוד הקיים → בנייה מחדש ב-Django (לא המרה אוטומטית).

---

## סטטוס נוכחי

**תאריך עדכון:** 2026-01-29

**שלב נוכחי:** 0 - תכנון ותשתית

**מה הושלם:**
- ✅ סקירת הפרויקט המקורי
- ✅ הגדרת מבנה תיעוד
- ✅ יצירת PLAN.md
- ✅ יצירת CONTEXT.md
- ✅ יצירת session-log.md
- ✅ יצירת docs/01-project-overview.md (סקירה מלאה)
- ✅ קבלת כל ההחלטות הנדרשות

**מה הבא:**
1. ליצור רפו חדש `kav-adom` ב-GitHub
2. לפתוח שיחה חדשה עם הרפו החדש
3. להתחיל חילוץ DB Schema

---

## הפרויקט המקורי בקצרה

**מאירים** - פלטפורמה להנגשת מידע על תכניות בנייה ועצים:

| רכיב | טכנולוגיה | מיקום |
|------|-----------|-------|
| Backend | Node.js 18, Express, Bookshelf ORM | `/server` |
| Frontend | React 17, Material-UI, Redux | `/client` |
| Database | MySQL 5.7 | - |
| Crawlers | Puppeteer (חלק מ-server) | `/server/api/lib/` |

**פיצ'רים עיקריים:**
- חיפוש וצפייה בתכניות בנייה
- התראות גיאוגרפיות למנויים
- היתרי כריתת עצים
- תגובות ודירוגים

**סטטיסטיקות קוד:**
- 27 controllers (~2,133 שורות)
- 35 models
- 13 סורקי עצים
- 11 תבניות email

---

## החלטות שהתקבלו

| נושא | החלטה | סיבה |
|------|--------|------|
| גישת המרה | אפיון + שכתוב | קוד נקי, Pythonic |
| תיעוד | markdown בתוך הרפו | נגיש, versioned |
| **מיקום רפו** | **רפו נפרד** | הפרדה נקייה |
| **שם הפרויקט** | **kav-adom** ("קו אדום") | שם חדש לפרויקט החדש |
| **Frontend** | **Django templates + HTMX** | פשטות, Python-only stack |
| **תאימות API** | **לא נדרשת** | להסתכל ביקורתית, לא לחזור על טעויות |

### פרטי שם הפרויקט
```
Repository name: kav-adom
Django project: kavadom
Python package: kavadom
Display name: קו אדום / Kav Adom
```

### טכנולוגיות Frontend שנבחרו
- **Django Templates** - לרינדור HTML
- **HTMX** - לאינטראקטיביות (AJAX ללא JS)
- **Alpine.js** - במידת הצורך לאינטראקציות קלות
- **Leaflet/Mapbox** - למפות (כמו במאירים)

---

## מבנה הקבצים

```
/home/user/meirim/
├── client/                    # Frontend מקורי (React)
├── server/                    # Backend מקורי (Node.js)
├── docs/                      # תיעוד מקורי
├── django-migration/          # 📁 תיעוד המיגרציה (אנחנו כאן)
│   ├── PLAN.md               # תכנית עבודה מפורטת
│   ├── CONTEXT.md            # קובץ זה
│   ├── docs/                 # תיעוד מפורט
│   ├── specs/                # מפרטים טכניים
│   └── progress/             # לוגים
└── [django-project]/          # 📁 הפרויקט החדש (יווצר)
```

---

## קבצי מקור חשובים

### Backend
| קובץ | תיאור | גודל |
|------|-------|------|
| `server/api/model/plan.js` | מודל תכניות | ~18KB |
| `server/api/model/person.js` | מודל משתמשים | ~6.5KB |
| `server/api/model/alert.js` | מודל התראות | ~5.4KB |
| `server/api/controller/cron.js` | לוגיקת jobs | ~18KB |
| `server/api/lib/mavat/index.js` | סורק מבא"ת | ~13KB |
| `server/api/apiRoutes.js` | כל ה-routes | ~112 שורות |

### Frontend
| קובץ | תיאור |
|------|-------|
| `client/src/pages/` | 10 דפים ראשיים |
| `client/src/services/api.js` | HTTP client |
| `client/src/store.js` | Redux store |

---

## איך להמשיך

### בשיחה הבאה, אמור:

```
"בוא נמשיך עם המיגרציה ל-Django.
קרא את CONTEXT.md ואת session-log.md והמשך מהנקודה האחרונה."
```

### או אם יש נושא ספציפי:

```
"בוא נמשיך עם המיגרציה. רוצה להתמקד ב-[נושא ספציפי]."
```

---

## קישורים למסמכים

- [תכנית עבודה מפורטת](./PLAN.md)
- [לוג שיחות](./progress/session-log.md)
- [סקירת פרויקט](./docs/01-project-overview.md) ✅
- [סכמת DB](./docs/02-database-schema.md) (יווצר)

---

## איך להמשיך ברפו החדש

בשיחה הראשונה ברפו `kav-adom`, אמור:

```
אני ממשיך פרויקט מיגרציה מ-Node.js ל-Django.
התיעוד המלא נמצא ברפו meirim בתיקייה django-migration/.
הנה הקבצים הרלוונטיים:
[העתק את תוכן CONTEXT.md]
```

או אם יש לך גישה לשני הרפוזיטוריות, פשוט הפנה אותי לקרוא את הקבצים.

---

*עודכן לאחרונה: 2026-01-29 | שיחה: #1 (סיום)*
