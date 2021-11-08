"""
Routes and views for the flask application.
"""

from datetime import datetime
from flask import render_template, request, redirect, url_for,session
from InsectDetection import app
import cv2
import os
import matplotlib.pyplot as plt
import pypyodbc as py
from flask_session import Session

# creating connection Object which will contain SQL Server Connection    
connection = py.connect('Driver={SQL Server Native Client 11.0};Server=DESKTOP-RE4AV47\SQLEXPRESS;Database=InsectDetection;uid=sa;pwd=sa@123')
                # Creating Cursor    
loggedUserName=''
loggedInUserId=0
app.config["SESSION_PERMANENT"] = False
app.config["SESSION_TYPE"] = "filesystem"
Session(app)
@app.route('/')
@app.route('/home')
def home():
    """Renders the home page."""
    return render_template(
        'index.html',
        title='Home Page',
        year=datetime.now().year,
    )
@app.route('/logout')
def logout():
    session['username']=""
    return redirect(url_for('home'))

@app.route('/contact')
def contact():
    """Renders the contact page."""
    return render_template(
        'contact.html',
        title='Contact',
        year=datetime.now().year,
        message='Your contact page.'
    )

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        cursor = connection.cursor()
        strQuery="INSERT INTO tbl_Users ([FirstName],[LastName],[Email],[MobileNumber],[Password],[IsActive]) VALUES ('"+request.form['firstName']+"', '"+request.form['lastName']+"','"+request.form['email']+"' ,'"+request.form['mobile']+"', '"+request.form['password']+"', 1)"
        print(strQuery)
        cursor.execute(strQuery)
        connection.commit()         
        return render_template(
        'register.html',
        message="Registration Success Please click on login to proceed!"
    )
    return render_template(
        'register.html',
        message=""
    )
    
    return render_template(
        'register.html'
    )

@app.route('/about')
def about():
    """Renders the about page."""
    return render_template(
        'about.html',
        title='Insect Identification using Image Processing',
        year=datetime.now().year,
        message='Below are the input and output images'
    )

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        
         cursor = connection.cursor()
         strQuery="select FirstName +' '+LastName as FullName from [tbl_Users] where Email='"+request.form['email']+"' and Password='"+request.form['password']+"'"
         print(strQuery)
         cursor.execute(strQuery)
         rows = cursor.fetchone()
         print(rows)
         if rows is None:
            return render_template('login.html', message="Invalid login details! Please try again")         
         else:
            for r in rows:
                session["username"] = r
                return redirect(url_for('upload_file'))
    else:
         return render_template('login.html')
UPLOAD_FOLDER = r'E:\InsectDetection\InsectDetection\InsectDetection\InsectDetection\static'


app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

@app.route('/upload_file', methods=['GET', 'POST'])
def upload_file():
    if not session.get("username"):
        print(session.get("username"))
        # if not there in the session then redirect to the login page
        return redirect(url_for("login"))
    loggedUserName=session.get("username")
    if request.method == 'POST':
        if 'file1' not in request.files:
            return 'there is no file1 in form!'
        file1 = request.files['file1']
        path = os.path.join(app.config['UPLOAD_FOLDER'], file1.filename)
        file1.save(path)
        inputPath=r'E:\InsectDetection\InsectDetection\InsectDetection\InsectDetection\static\input.jpg'
        img_rgb = cv2.imread(inputPath,0)

        img = cv2.imread(inputPath)
        gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
        ret, thresh = cv2.threshold(gray,150,255,cv2.THRESH_BINARY_INV+cv2.THRESH_OTSU)
        plt.imshow(thresh)
        plt.axis('off')
        def plotImg(img):
            if len(img.shape) == 2:
                plt.imshow(img, cmap='gray')
                plt.show()

            else:
                plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
                plt.show()
                plt.title('healthy leaf')

        img = cv2.imread(inputPath)
        gray_img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        binary_img = cv2.adaptiveThreshold(gray_img, 150, cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                                           cv2.THRESH_BINARY_INV, 31, 15)
        plotImg(binary_img)
        _, _, boxes, _ = cv2.connectedComponentsWithStats(binary_img)
        # first box is the background
        boxes = boxes[15:]
        filtered_boxes = []
        for x,y,w,h,pixels in boxes:
            if pixels < 10000 and h < 200 and w < 200 and h > 10 and w > 10:
                filtered_boxes.append((x,y,w,h))

        for x,y,w,h in filtered_boxes:
            cv2.rectangle(img, (x,y), (x+w,y+h), (0,0,255),1)

        plotImg(img)




        destPath=r'E:\InsectDetection\InsectDetection\InsectDetection\InsectDetection\static'
        cv2.imwrite(os.path.join(destPath, "Detected.jpg"), img)

        #cv2.imshow('Detected',img_rgb)

        cv2.imwrite("ReDetected.jpg",img_rgb)
        cv2.waitKey(0)
        cv2.destroyAllWindows()   


        return redirect(url_for('about'))

        return redirect(url_for('about'))
    return render_template('imageupload.html')
