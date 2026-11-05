IMAGE                              DISK USAGE     
shoplist-frontend:kitchen            172MB 
                                             
shoplist-frontend:multi             62.4MB 
                                               
shoplist-frontend:single              62.4MB 
<!-- תשובות -->
1. ל-backend אין ports. stats נמצא איתו באותה רשת פנימית של Compose ופונה אליו לפי שם ה-service: http://backend:5000. הדפדפן לא מגיע אליו ישירות, אלא פונה ל-frontend בפורט 8080, ו-Nginx מעביר את בקשות ה-/api/ ל-backend.

2. אחרי docker compose down ו-up המוצרים נשארים, כי הם שמורים ב-named volume (postgres-data), ו-down מוחק רק containers ורשת. docker compose down -v מוחק גם את ה-named volume, ואיתו את כל הנתונים.

3. זה bind mount. יודעים כי הצד השמאלי הוא נתיב במחשב (../sql/init.sql) ולא שם של volume שמוגדר תחת volumes:. אם משנים את init.sql אחרי שהמסד נוצר, לא יקרה כלום, כי הקובץ רץ רק כשה-volume ריק. כדי שהשינוי ייכנס צריך down -v ואז up.

4. הרשת: docker_default. ה-volume: docker_postgres-data. הקידומת docker היא שם הפרויקט, שנלקח משם התיקייה של קובץ ה-Compose. default היא הרשת ש-Compose יוצר אוטומטית, ו-postgres-data הוא השם מרשימת volumes: בקובץ.

5. ב-image הסופי יש רק nginx, הקבצים הבנויים מ-/app/dist ו-nginx.conf. אין בו Node, את build.js ואת קבצי המקור. אין CMD כי image הבסיס nginx כבר מגדיר CMD שמפעיל את nginx, והוא עובר בירושה. stats לא צריך multi-stage כי הוא צריך Node גם בזמן הריצה, ואין לו שלב בנייה שאפשר לזרוק.

6. single: 62.4 MB, multi: 62.4 MB, kitchen: 172.04 MB. multi כמעט בגודל של single כי שניהם מבוססים על nginx:1.30-alpine, וההבדל הוא רק הקבצים הבנויים, ששוקלים כמה KB. כל המשקל של Node נשאר בשלב build ולא נכנס ל-image הסופי.

7. Nginx בוחר את ה-location עם הקידומת הארוכה ביותר שמתאימה, ולכן /api/stats הולך ל-location /api/stats ולא ל-/api/. הלוכסן בסוף http://backend:5000/ מחליף את הקידומת /api/ ב-/, ולכן הבקשה /api/products מגיעה ל-backend כ-/products.
                                   