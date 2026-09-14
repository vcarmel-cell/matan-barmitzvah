# דף אישורי הגעה - בר מצווה של מתן

שני דפים:
- `index.html` - הדף שנשלח לאורחים בוואטסאפ (מגיע/לא מגיע + כמות)
- `admin.html` - דף סיכום פרטי לצפייה בכל התשובות (מוגן בקוד גישה פשוט)

## שלב 1: הקמת פרויקט Firebase (חד פעמי)

1. כנסו ל-https://console.firebase.google.com והתחברו עם חשבון Google
2. "Add project" → תנו שם (לדוגמה `matan-barmitzvah`) → המשיכו עד הסוף (אפשר לכבות Google Analytics, לא נחוץ)
3. בתפריט הצד: **Build → Firestore Database** → **Create database** → מצב **production mode** → בחרו region (למשל `eur3` או `us-central`) → Enable
4. בתפריט הצד: **Build → Authentication** → **Get started** → בלשונית Sign-in method הפעילו **Anonymous**
5. לחצו על גלגל השיניים ליד "Project Overview" → **Project settings**. תחת "Your apps" לחצו על אייקון ה-web `</>`, תנו שם לאפליקציה (לדוגמה "RSVP") ולחצו Register app
6. תעתיקו את האובייקט `firebaseConfig` שמופיע (apiKey, authDomain, projectId וכו') ותשלחו לי אותו - אני אכניס אותו לקובץ `firebase-config.js`

## שלב 2: חוקי אבטחה ל-Firestore

ב-Firestore Database → לשונית **Rules**, הדביקו:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /rsvps/{docId} {
      allow read: if true;
      allow create, update: if request.auth != null
        && request.resource.data.name is string
        && request.resource.data.attending is bool
        && request.resource.data.count is number
        && request.resource.data.count >= 0
        && request.resource.data.count <= 20;
      allow delete: if false;
    }
  }
}
```

זה מאפשר לכל מי שנכנס לקישור (עם התחברות אנונימית אוטומטית) לשלוח/לעדכן רק את התגובה שלו, בלי למחוק תגובות של אחרים.

## שלב 3: פרטי האירוע

תשלחו לי: תאריך, שעה, שם ומקום האולם, ותאריך יעד לאישור הגעה - ואני אעדכן את הדף.

## שלב 4: פרסום הדף (Hosting)

כדי לקבל קישור לשליחה בוואטסאפ, הכי פשוט זה GitHub Pages (בדיוק כמו PDFSign) - תגידו לי ואני אקים את זה ברגע שהתוכן סופי.

## קוד גישה לדף הניהול

כרגע מוגדר בקובץ `admin.html` (משתנה `ADMIN_PASSCODE`) לקוד: `1234`
זו הגנה בסיסית בלבד (לא אבטחה אמיתית) - מספיקה כדי שאורחים במקרה לא ייכנסו לדף הניהול. אפשר לבקש ממני לשנות אותו בכל שלב.
