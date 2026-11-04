# מתחילים מתמונת בסיס שמכילה Python.
FROM python:3.11-slim
# קובעים את תיקיית העבודה בתוך סביבת Docker.
WORKDIR /app
# מעתיקים את קובץ התלויות לתיקיית העבודה.
COPY requirements.txt /app/
# מתקינים את הספריות מתוך הקובץ.
RUN pip install --no-cache-dir -r requirements.txt
# מעתיקים את קוד התוכנית לתיקיית העבודה.
COPY app.py .
# מציינים שהשירות מאזין בפורט 8000 בתוך הקונטיינר.
EXPOSE 8000
# מגדירים את הפקודה שתופעל כשהקונטיינר יתחיל לרוץ.
CMD ["python","app.py"]