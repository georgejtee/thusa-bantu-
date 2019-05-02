from PyQt4 import QtCore, QtGui

try:
    _fromUtf8 = QtCore.QString.fromUtf8
except AttributeError:
    def _fromUtf8(s):
        return s
import os,sys
import sqlite3
import numpy as np
import threading
import cv2
import time
import smtplib
import subprocess as sp

import zipfile
try:
    _encoding = QtGui.QApplication.UnicodeUTF8
    def _translate(context, text, disambig):
        return QtGui.QApplication.translate(context, text, disambig, _encoding)
except AttributeError:
    def _translate(context, text, disambig):
        return QtGui.QApplication.translate(context, text, disambig)
class compression():
    def delete(self):
        os.system("del /f /q "+os.getcwd()+"\\video\\"+ui.listWidget.currentItem().text())
        fO.feel()
    
    def other_move(self):
         os.system("move "+self.p+"\\compressed\\"+ui.listWidget.currentItem().text()+" "+self.p+"\\uncompressed\\"+ui.listWidget.currentItem().text())
         self.decompress()
    def move(self):
        self.p=os.getcwd()
        print(self.p+"move ./video//\\"+ui.listWidget.currentItem().text())
        os.system("move "+self.p+"\\video\\"+ui.listWidget.currentItem().text()+" "+self.p+"\\compressed\\"+ui.listWidget.currentItem().text())
        self.compress()
    def compress(self):
        jungle_zip = zipfile.ZipFile(self.p+"\\compressed\\"+ui.listWidget.currentItem().text()+".zip", 'w')
        jungle_zip.write(self.p+"\\compressed\\"+ui.listWidget.currentItem().text(), compress_type=zipfile.ZIP_DEFLATED)
        
        jungle_zip.close()
        os.system("del "+self.p+"\\compressed\\"+ui.listWidget.currentItem().text())
        fO.feel()
        
    def decompress(self):
        z=0
        s=""
        for i in ui.listWidget.currentItem().text():
            if i==".":
                z+=1
            if z==2:
                break
            s+=i
        fantasy_zip = zipfile.ZipFile(self.p+'.\\uncompressed\\'+ui.listWidget.currentItem().text(),'r')
        fantasy_zip.extract('\\compressed\\'+s, '.\\uncompressed\\')
         
        fantasy_zip.close()
        fO.feel()
        
class notifications():
    def login(self):
        self.server = smtplib.SMTP('smtp.gmail.com', 587)
        try:
            print("try")
            
            self.server.starttls()
            self.server.login("jarai.samuel@gmail.com", "iphone6plus")
        except:
            print("fail")
            time.sleep(5)
            self.login()
    def sendMail(self):
        fO.ss=99
        msg = "10 notifications reached at "
        tt=time.asctime(time.localtime(time.time()))
        tt=str(tt)
        s=str()
        for i in tt:
            if i!=":":
                s+=i
            if i==":":
                s+=";"
        msg+=s
        print(ac.mailList)
        for i in ac.mailList:
            print(i)
            for a in i:
                print(a)
                self.server.sendmail("jarai.samuel@gmail.com",a,"'"+ msg+"'")
                ui.listWidget_12.addItem(msg)
                ui.listWidget_12.addItem("mail sent to:{}".format(a))
        fO.ss=0
        
        
        
class accounts():
    def setUp(self):
        self.con=sqlite3.connect("main.db")
        self.cur=self.con.cursor()
        self.loadUsers()


    def loadUsers(self):
        ui.listWidget_2.clear()
        self.cur.execute("select username from users")
        self.user=self.cur.fetchall()
        self.cur.execute("select username from admin")
        self.admin=self.cur.fetchall()
        self.cur.execute("select email from admin")
        self.email=self.cur.fetchall()
        self.cur.execute("select email from users")
        self.email1=self.cur.fetchall()
        self.mailList=self.email+self.email1
        self.allUsers=self.user+self.admin
        for i in self.allUsers:
            for a in i:
                ui.listWidget_2.addItem(a)
        print(self.allUsers)
    def delete(self):
        
        acc=ui.listWidget_2.currentItem().text()
        for i in self.user:
            for a in i:
                if(acc==a):
                    self.cur.execute("delete from users where username='"+acc+"'")
                    self.con.commit()
        for i in self.admin:
            for a in i:
                if(acc==a):
                    self.cur.execute("delete from admin where username='"+acc+"'")
                    self.con.commit()
        self.loadUsers()
    def create(self):
        x=0
        y=0
        for row in self.admin:
            y=0
            for item in row:
                if(item==ui.lineEdit.text()):
                    
                    
                    print("User "+item+" already exists")
                    return
                y=y+1
            x=x+1
        x=0
        y=0
        for row in self.user:
            y=0
            for item in row:
                if(item==ui.lineEdit.text()):
                    print("User "+item+" already exists")
                    return
                    

                y=y+1
            x=x+1
        aa=ui.lineEdit_3.text()
        ab=ui.lineEdit_4.text()
        ac=ui.lineEdit.text()
        ad=ui.lineEdit_2.text()
        if(ab!=aa):
            ui.lineEdit_3.setText("Passwords do not match")
            ui.lineEdit_4.setText("")
            return
        if(ac=="" or ad=="" or aa=="" or ab==""):
            print("fee")
            return
        print("here")
        zz=0
        yy=0
        for i in ui.lineEdit_2.text():
            if i=="@":
                zz+=1
                for i in ui.lineEdit_2.text():
                    if i==".":
                        yy+=1
        if zz>0 and yy>0:
            pass
        else:
            ui.lineEdit_2.setText("Invalid Email")
            return
                
        if ui.checkBox_3.isChecked()==True and ui.checkBox_4.isChecked()==True:
            print("one at a time")
        elif(ui.checkBox_3.isChecked()==False and ui.checkBox_4.isChecked()==False):
            print("pick something")
        elif(ui.checkBox_3.isChecked()==True):
            try:
                print("hfh")
                self.cur.execute("insert into admin('username','email','password') values('"+ac+"','"+ad+"','"+aa+"')")
                self.con.commit()
                self.loadUsers()
            except:
                print("failed to commit")
        elif(ui.checkBox_4.isChecked()==True):
            try:
                self.cur.execute("insert into users('username','email','password') values('"+ac+"','"+ad+"','"+aa+"')")
                self.con.commit()
                self.loadUsers()
            except:
                print("failed to commit")
        print("too")
        
class new_Feed():
    def setUpVars(self):
        self.t=threading.Lock()
        self.frame=None
        self.roi_gray=None
        self.state=0
        self.ss=0
        self.face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
        self.pface_cascade = cv2.CascadeClassifier('haarcascade_profileface.xml')
        self.uface_cascade = cv2.CascadeClassifier('haarcascade_upperbody.xml')
        self.xface_cascade = cv2.CascadeClassifier('haarcascade_fullbody.xml')
        self.zface_cascade = cv2.CascadeClassifier('haarcascade_lowerbody.xml')
        self.duration=5
        self.dir="./video/"
        if tool.vid_path!="":
            self.dir=tool.vid_path
        self.haar=""
        self.footage=""
        self.stream_var=0
        t1=threading.Thread(target=self.stream)
        t1.start()
        self.setUp()

    def status(self):
        if ui.checkBox_2.isChecked()==True:
            self.stream_var=1
        else:
            self.stream_var=0
    def stream(self):
        self.cap=cv2.VideoCapture(0)
        while(True):
            if self.stream_var==1:
                self.cap.release()
                while 1==1:
                    time.sleep(1)
                    if self.stream_var==0:
                        self.stream()
            ret, self.pic=self.cap.read()
            self.img=self.pic
            self.frame=self.img
            
            self.gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
    def recorded(self):
        while 1==1:
            print("hiet")
            if self.state==0:
                return
            
            ret,self.frame=self.cap.read()
            self.img=self.frame
            self.gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            self.pic=self.img
            time.sleep(0.07)
            print("fraud")
    def watch(self):
        t1=threading.Thread(target=self.watch_thread)
        t1.start()
    def start(self):
        self.duration=5
        self.dir=""
        self.haar=""
        self.footage=""
        self.path
        if tool.vid_length!="null":
            self.duration=int(tool.vid_length)
        
        if tool.haar_path!="null":
            self.haar=tool.haar_path
        if tool.custom_vid!="null":
            self.footage=tool.custom_vid
        if ui.checkBox_2.isChecked()==True:
            self.cap=None
            self.cap=cv2.VideoCapture(self.footage)
            print(self.footage)
        
            
        
        print(self.dir)    
        #self.s=1   
        self.state=1
        self.s1=ui.horizontalSlider.value()
        self.s2=ui.horizontalSlider_2.value()
        self.s3=ui.horizontalSlider_3.value()
        
        self.t1=threading.Thread(target=self.face)
        self.t2=threading.Thread(target=self.pface)
        self.t4=threading.Thread(target=self.upper)
        self.t3=threading.Thread(target=self.show)
        self.t5=threading.Thread(target=self.lower)
        self.t6=threading.Thread(target=self.full)
        self.t7=threading.Thread(target=self.clock)
        self.t8=threading.Thread(target=self.motionDetection)
        self.t9=threading.Thread(target=self.recorded)
        self.t10=threading.Thread(target=self.custom)
        if ui.checkBox_2.isChecked()==True:
            self.t9.start()
        if ui.checkBox.isChecked()==True:
            self.t10.start()
            time.sleep(2)
        if self.s3==1 or self.s3==2:
            self.t8.start()
        if(self.s1==1):
            
            self.t1.start()
        elif(self.s1==2):
            
            self.t2.start()
            self.t1.start()
        if(self.s2==1):
            
            self.t4.start()
        elif(self.s2==2):
            print("yah")
            self.t5.start()
            self.t6.start()
            self.t4.start()
            
        self.t7.start()    
        self.t3.start()
    def clock(self):
        ti=0
        
        while(True):
            ui.lcdNumber.display(ti)
            ti+=1
            time.sleep(1)
            if(self.state==0):
                return
    def stop(self):
        self.s=0
        self.ss=0
        self.state=0
        self.s1=None
        self.s2=None
        self.s3=None
        
        self.t1=None
        self.t2=None
        self.t4=None
        self.t3=None
        self.t5=None
        self.t6=None
        self.t7=None
        self.t8=None
        self.t9=None
        self.t10=None
        #cv2.destroyAllWindows()
        
        
    def custom(self):
        try:
            self.custom_cascade = cv2.CascadeClassifier(self.haar)

        
            while(True):
                if(self.state==0):
                    return
                xf = self.custom_cascade.detectMultiScale(self.gray, 1.3, 5)
                #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
                #faces = self.face_cascade.detectMultiScale(gray, 1.3, 5)
                #pfaces = self.pface_cascade.detectMultiScale(gray, 1.3, 5)
                #ufaces = self.uface_cascade.detectMultiScale(self.gray, 1.3, 5)
                for (x, y, w, h) in xf:
                    cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 0, 255), 2)
                    self.kickStart()
        except:
            pass

        
    def noChange(self):
        try:
            ui.horizontalSlider.setValue(self.s1)
            ui.horizontalSlider_2.setValue(self.s2)
            ui.horizontalSlider_3.setValue(self.s3)
        except:
            pass
        
    def watch_thread(self):
        while(True):
            
            cv2.putText(self.frame,'Live',(5,50),self.font,1,(0,0,255),2,cv2.LINE_AA)
            cv2.imshow("Live Stream",self.frame)
            if cv2.waitKey(1) & 0xFF==ord('q'):
                break
    def motionDetection(self):
        frame_now = self.cap.read()[1]
        frame_now = self.cap.read()[1]
        frame_now = cv2.cvtColor(frame_now, cv2.COLOR_RGB2GRAY)
        #frame_now = cv2.blur(frame_now, (BLUR_SIZE, BLUR_SIZE))
        frame_prior = frame_now

        delta_count_last = 1
        if 1==1:
            
            if self.s3==1:
                while True:
                    if self.state==0:
                        return
                    frame_delta = cv2.absdiff(frame_prior, frame_now)
                    frame_delta = cv2.threshold(frame_delta, 50, 255, 3)[1]
                    delta_count = cv2.countNonZero(frame_delta)

                    # Visual detection statistics output.
                    # Normalize improves brightness and contrast.
                    # Mirror view makes self display more intuitive.
                    cv2.normalize(frame_delta, frame_delta, 0, 255, cv2.NORM_MINMAX)
                    frame_delta = cv2.flip(frame_delta, 1)
                    cv2.putText(frame_delta, "DELTA: %d" % (delta_count),
                            (5, 15), cv2.FONT_HERSHEY_PLAIN, 0.8, (255, 255, 255))
                    #cv2.imshow("window_name", frame_delta)

                    # Stdout output.
                    # Only output when there is new movement or when movement stops.
                    # Time codes are in epoch time format.
                    """
                    if (delta_count_last == 0 and delta_count != 0):
                        sys.stdout.write("MOVEMENT %f\n" % time.time())
                        sys.stdout.flush()
                    elif delta_count_last != 0 and delta_count == 0:
                        sys.stdout.write("STILL    %f\n" % time.time())
                        sys.stdout.flush()"""
                    if delta_count>2000:
                        self.kickStart()
                    delta_count_last = delta_count

                    # Advance the frames.
                    frame_prior = frame_now
                    frame_now = self.cap.read()[1]
                    frame_now = cv2.cvtColor(frame_now, cv2.COLOR_RGB2GRAY)
                   # frame_now = cv2.blur(frame_now, (BLUR_SIZE, BLUR_SIZE))

                    # Wait up to 10ms for a key press. Quit if the key is either ESC or 'q'.
                    key = cv2.waitKey(10)
                    if key == 0x1b or key == ord('q'):
                        cv2.destroyWindow(window_name)
                        break
                    
            elif(self.s3==2):
                while True:
                    if self.state==0:
                        return
                    frame_delta = cv2.absdiff(frame_prior, frame_now)
                    frame_delta = cv2.threshold(frame_delta, 50, 255, 3)[1]
                    delta_count = cv2.countNonZero(frame_delta)

                    # Visual detection statistics output.
                    # Normalize improves brightness and contrast.
                    # Mirror view makes self display more intuitive.
                    cv2.normalize(frame_delta, frame_delta, 0, 255, cv2.NORM_MINMAX)
                    frame_delta = cv2.flip(frame_delta, 1)
                    cv2.putText(frame_delta, "DELTA: %d" % (delta_count),
                            (5, 15), cv2.FONT_HERSHEY_PLAIN, 0.8, (255, 255, 255))
                    #cv2.imshow("window_name", frame_delta)
                    
                    # Stdout output.
                    # Only output when there is new movement or when movement stops.
                    # Time codes are in epoch time format.
                    """
                    if (delta_count_last == 0 and delta_count != 0):
                        sys.stdout.write("MOVEMENT %f\n" % time.time())
                        sys.stdout.flush()
                    elif delta_count_last != 0 and delta_count == 0:
                        sys.stdout.write("STILL    %f\n" % time.time())
                        sys.stdout.flush()"""
                    if delta_count>100:
                        self.kickStart()
                    delta_count_last = delta_count

                    # Advance the frames.
                    frame_prior = frame_now
                    frame_now = self.cap.read()[1]
                    frame_now = cv2.cvtColor(frame_now, cv2.COLOR_RGB2GRAY)
                   # frame_now = cv2.blur(frame_now, (BLUR_SIZE, BLUR_SIZE))

                    # Wait up to 10ms for a key press. Quit if the key is either ESC or 'q'.
                    key = cv2.waitKey(10)
                    if key == 0x1b or key == ord('q'):
                        cv2.destroyWindow(window_name)
                        break
    def pface(self):
        while(True):
            if(self.state==0):
                return
            #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            #faces = self.face_cascade.detectMultiScale(gray, 1.3, 5)
            pfaces = self.pface_cascade.detectMultiScale(self.gray, 1.3, 5)
            #ufaces = self.uface_cascade.detectMultiScale(gray, 1.3, 5)
            for (x, y, w, h) in pfaces:
                cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 0, 0), 2)
                self.kickStart()
                #roi_gray=gray[y:y+h,x:x+w]
                #roi_img=img[y:y+h,x:x+w]
                #self.rec()

            """cv2.imshow('img', self.frame)
            if cv2.waitKey(1) & 0xFF==ord('q'):
                break"""
    def face(self):
        
        while(True):
            if(self.state==0):
                return
            #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            faces = self.face_cascade.detectMultiScale(self.gray, 1.3, 5)
            #pfaces = self.pface_cascade.detectMultiScale(gray, 1.3, 5)
            #ufaces = self.uface_cascade.detectMultiScale(gray, 1.3, 5)
            for (x, y, w, h) in faces:
                cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 255, 0), 2)
                self.kickStart()
                #self.roi_gray=gray[y:y+h,x:x+w]
                #roi_img=img[y:y+h,x:x+w]
                #self.rec()

            """cv2.imshow('img', self.frame)
            if cv2.waitKey(1) & 0xFF==ord('q'):
                break"""
    def upper(self):
        
        while(True):
            if(self.state==0):
                return
            #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            #faces = self.face_cascade.detectMultiScale(gray, 1.3, 5)
            #pfaces = self.pface_cascade.detectMultiScale(gray, 1.3, 5)
            ufaces = self.uface_cascade.detectMultiScale(self.gray, 1.3, 5)
            for (x, y, w, h) in ufaces:
                cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 0, 255), 2)
                self.kickStart()
    def full(self):
        
        while(True):
            if(self.state==0):
                return
            xfaces = self.xface_cascade.detectMultiScale(self.gray, 1.3, 5)
            #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            #faces = self.face_cascade.detectMultiScale(gray, 1.3, 5)
            #pfaces = self.pface_cascade.detectMultiScale(gray, 1.3, 5)
            #ufaces = self.uface_cascade.detectMultiScale(self.gray, 1.3, 5)
            for (x, y, w, h) in xfaces:
                cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 0, 255), 2)
                self.kickStart()
    def lower(self):
        
        while(True):
            if(self.state==0):
                return
            zfaces = self.zface_cascade.detectMultiScale(self.gray, 1.3, 5)
            #gray = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            #faces = self.face_cascade.detectMultiScale(gray, 1.3, 5)
            #pfaces = self.pface_cascade.detectMultiScale(gray, 1.3, 5)
            #ufaces = self.uface_cascade.detectMultiScale(self.gray, 1.3, 5)
            for (x, y, w, h) in zfaces:
                cv2.rectangle(self.frame, (x, y), (x + w, y + h), (255, 0, 255), 2)
                self.kickStart()

            
    def show(self):
        print("gg")
        while(True):
            if(self.state==0):
                cv2.destroyAllWindows()
                return
            cv2.imshow("Live Stream",self.frame)
            if cv2.waitKey(1) & 0xFF==ord('q'):
                break

    def kickStart(self):
        if self.s==1:
            return
        if self.ss>10:
            t=None
            t=threading.Thread(target=n.sendMail)
            t.start()
        
            
        ta=None
        tz=None
        ta=threading.Thread(target=self.record)
        tz=threading.Thread(target=self.timer)
        ta.start()
        tz.start()
        if self.ss==99:
            return
        self.ss+=1
    def record(self):
        self.t.acquire()
    
        print("record")
        self.s=1
        self.sam+=1
        name=str(self.sam)
        p=self.path+'/video/'+name+'.mp4'
        out=cv2.VideoWriter(p,self.fourcc,150.0,(640,480))
        while True:
            cv2.putText(self.frame,time.asctime(time.localtime(time.time())),(5,50),self.font,0.8,(0,255,0),2,cv2.LINE_AA)
            out.write(self.frame)
            if(self.s==0):
                return
    def timer(self):
        time.sleep(self.duration)
        self.s=0
        self.t.release()
        
        self.feel()
    def setUp(self):
        self.path1=self.dir
        files = sorted(os.listdir(self.path1), key=lambda x: os.path.getctime(os.path.join(self.path1, x)))
        #self.capp = cv2.VideoCapture('1111.mp4')
        #x=input(files)
        #print(files)

        
        
        #self.dirt=os.listdir(self.path1)
        x=len(files)
        x=x-1
        gold=files[x]
        m=len(gold)
        m=m-4
        diamond=gold[:m]
        self.sam=int(diamond)

       # self.t0=threading.Thread(target=self.start)


        self.clockVar=0
        self.s=0
        self.path=os.getcwd()
        self.font=cv2.FONT_HERSHEY_SIMPLEX
        self.fourcc=cv2.VideoWriter_fourcc(*'MP4V')
        
        

        
    def feel(self):
        ui.listWidget.clear()
        self.dirt=sorted(os.listdir(self.path1), key=lambda x: os.path.getctime(os.path.join(self.path1, x)))
       
        for x in self.dirt:
            
            ui.listWidget.addItem(x)
        self.ppp=os.getcwd()+"\\compressed\\"
        """self.dirt=sorted(os.listdir(self.ppp), key=lambda x: os.path.getctime(os.path.join(self.path1, x)))
        
        ui.listWidget.addItem("---------Compressed--------")
        for x in self.dirt:
            
            ui.listWidget.addItem(x)"""
   
        
    def playBack(self):
        video=ui.listWidget.currentItem().text()
        v=self.path1+video
        c=cv2.VideoCapture(v)
        #print("1")
        while(c.isOpened()):
            #print("2")
            ret, frame=c.read()
            cv2.imshow("Playback",frame)
            if cv2.waitKey(1) & 0xFF==ord('q'):
                break

class Settings():
    def setUp(self):
        self.vid_length=0
        self.vid_path=""
        self.haar_path=""
        self.custom_vid=""
    def retrieve(self):
        self.config=list()
        f=open("system.bin","r").read()
        for i in f.split(";"):
            self.config.append(i)
        n=0
        for i in self.config:
            if n==0 and self.config[0]!="null":
                self.vid_length=int(self.config[0])
            elif n==0 and self.config[0]=="null":
                self.vid_length=str(self.vid_length)
            elif n==1 and self.config[1]!="null":
                self.vid_path=self.config[1]
            elif n==2 and self.config[2]!="null":
                self.haar_path=self.config[2]
            elif n==3 and self.config[3]!="null":
                self.custom_vid=self.config[3]
            n+=1
        #f.close()
        print(self.vid_length)
        print(self.vid_path)
        print(self.haar_path)
        print(self.custom_vid)
    def save(self):
        a=ui.lineEdit_9.text()
        b=ui.lineEdit_5.text()
        c=ui.lineEdit_6.text()
        d=ui.lineEdit_7.text()
        if a=="":
            a="null"
        if b=="":
            b="null"
        if c=="":
            c="null"
        if d=="":
            d="null"
        file=open("system.bin","w")
        file.write(a+";"+b+";"+c+";"+d+";")
        file.close()
    def restoreDefaults(self):
        file=open("system.bin","w")
        file.write("null;null;null;null")
        file.close()
        
class Ui_Dialog(object):
    def setupUi(self, Dialog):
        Dialog.setObjectName(_fromUtf8("Dialog"))
        Dialog.resize(815, 528)
        self.tabWidget = QtGui.QTabWidget(Dialog)
        self.tabWidget.setGeometry(QtCore.QRect(0, 0, 811, 531))
        self.tabWidget.setObjectName(_fromUtf8("tabWidget"))
        self.tab = QtGui.QWidget()
        self.tab.setObjectName(_fromUtf8("tab"))
        self.pushButton_9 = QtGui.QPushButton(self.tab)
        self.pushButton_9.setGeometry(QtCore.QRect(700, 460, 75, 23))
        self.pushButton_9.setObjectName(_fromUtf8("pushButton_9"))
        self.label = QtGui.QLabel(self.tab)
        self.label.setGeometry(QtCore.QRect(10, 90, 81, 20))
        self.label.setObjectName(_fromUtf8("label"))
        self.label_4 = QtGui.QLabel(self.tab)
        self.label_4.setGeometry(QtCore.QRect(440, 30, 131, 16))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.label_4.setFont(font)
        self.label_4.setObjectName(_fromUtf8("label_4"))
        self.label_3 = QtGui.QLabel(self.tab)
        self.label_3.setGeometry(QtCore.QRect(0, 210, 81, 20))
        self.label_3.setObjectName(_fromUtf8("label_3"))
        self.pushButton_2 = QtGui.QPushButton(self.tab)
        self.pushButton_2.setGeometry(QtCore.QRect(200, 30, 75, 23))
        self.pushButton_2.setObjectName(_fromUtf8("pushButton_2"))
        self.pushButton_3 = QtGui.QPushButton(self.tab)
        self.pushButton_3.setGeometry(QtCore.QRect(110, 30, 75, 23))
        self.pushButton_3.setObjectName(_fromUtf8("pushButton_3"))
        self.pushButton_13 = QtGui.QPushButton(self.tab)
        self.pushButton_13.setGeometry(QtCore.QRect(10, 470, 75, 23))
        self.pushButton_13.setObjectName(_fromUtf8("pushButton_13"))
        self.pushButton_4 = QtGui.QPushButton(self.tab)
        self.pushButton_4.setGeometry(QtCore.QRect(440, 460, 75, 23))
        self.pushButton_4.setObjectName(_fromUtf8("pushButton_4"))
        self.listWidget = QtGui.QListWidget(self.tab)
        self.listWidget.setGeometry(QtCore.QRect(425, 70, 351, 371))
        self.listWidget.setObjectName(_fromUtf8("listWidget"))
        self.pushButton_5 = QtGui.QPushButton(self.tab)
        self.pushButton_5.setGeometry(QtCore.QRect(570, 460, 75, 23))
        self.pushButton_5.setObjectName(_fromUtf8("pushButton_5"))
        self.label_5 = QtGui.QLabel(self.tab)
        self.label_5.setGeometry(QtCore.QRect(10, 340, 131, 16))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.label_5.setFont(font)
        self.label_5.setObjectName(_fromUtf8("label_5"))
        self.horizontalSlider = QtGui.QSlider(self.tab)
        self.horizontalSlider.setGeometry(QtCore.QRect(90, 90, 261, 22))
        self.horizontalSlider.setOrientation(QtCore.Qt.Horizontal)
        self.horizontalSlider.setObjectName(_fromUtf8("horizontalSlider"))
        self.lcdNumber = QtGui.QLCDNumber(self.tab)
        self.lcdNumber.setGeometry(QtCore.QRect(730, 0, 64, 23))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.lcdNumber.setFont(font)
        self.lcdNumber.setObjectName(_fromUtf8("lcdNumber"))
        self.pushButton_8 = QtGui.QPushButton(self.tab)
        self.pushButton_8.setGeometry(QtCore.QRect(290, 30, 75, 23))
        self.pushButton_8.setObjectName(_fromUtf8("pushButton_8"))
        self.pushButton_12 = QtGui.QPushButton(self.tab)
        self.pushButton_12.setGeometry(QtCore.QRect(10, 430, 75, 23))
        self.pushButton_12.setObjectName(_fromUtf8("pushButton_12"))
        self.pushButton = QtGui.QPushButton(self.tab)
        self.pushButton.setGeometry(QtCore.QRect(10, 30, 75, 23))
        self.pushButton.setObjectName(_fromUtf8("pushButton"))
        self.listView = QtGui.QListView(self.tab)
        self.listView.setGeometry(QtCore.QRect(10, 370, 371, 51))
        self.listView.setObjectName(_fromUtf8("listView"))
        self.label_2 = QtGui.QLabel(self.tab)
        self.label_2.setGeometry(QtCore.QRect(10, 150, 81, 20))
        self.label_2.setObjectName(_fromUtf8("label_2"))
        self.horizontalSlider_2 = QtGui.QSlider(self.tab)
        self.horizontalSlider_2.setGeometry(QtCore.QRect(90, 150, 261, 22))
        self.horizontalSlider_2.setOrientation(QtCore.Qt.Horizontal)
        self.horizontalSlider_2.setObjectName(_fromUtf8("horizontalSlider_2"))
        self.horizontalSlider_3 = QtGui.QSlider(self.tab)
        self.horizontalSlider_3.setGeometry(QtCore.QRect(90, 210, 261, 22))
        self.horizontalSlider_3.setOrientation(QtCore.Qt.Horizontal)
        self.horizontalSlider_3.setObjectName(_fromUtf8("horizontalSlider_3"))
        self.checkBox = QtGui.QCheckBox(self.tab)
        self.checkBox.setGeometry(QtCore.QRect(90, 300, 121, 17))
        self.checkBox.setObjectName(_fromUtf8("checkBox"))
        self.checkBox_2 = QtGui.QCheckBox(self.tab)
        self.checkBox_2.setGeometry(QtCore.QRect(250, 300, 141, 17))
        self.checkBox_2.setObjectName(_fromUtf8("checkBox_2"))
        self.label_17 = QtGui.QLabel(self.tab)
        self.label_17.setGeometry(QtCore.QRect(80, 120, 47, 13))
        self.label_17.setObjectName(_fromUtf8("label_17"))
        self.label_18 = QtGui.QLabel(self.tab)
        self.label_18.setGeometry(QtCore.QRect(210, 120, 47, 13))
        self.label_18.setObjectName(_fromUtf8("label_18"))
        self.label_19 = QtGui.QLabel(self.tab)
        self.label_19.setGeometry(QtCore.QRect(340, 120, 47, 13))
        self.label_19.setObjectName(_fromUtf8("label_19"))
        self.label_20 = QtGui.QLabel(self.tab)
        self.label_20.setGeometry(QtCore.QRect(340, 180, 47, 13))
        self.label_20.setObjectName(_fromUtf8("label_20"))
        self.label_21 = QtGui.QLabel(self.tab)
        self.label_21.setGeometry(QtCore.QRect(80, 180, 47, 13))
        self.label_21.setObjectName(_fromUtf8("label_21"))
        self.label_22 = QtGui.QLabel(self.tab)
        self.label_22.setGeometry(QtCore.QRect(210, 180, 47, 13))
        self.label_22.setObjectName(_fromUtf8("label_22"))
        self.label_23 = QtGui.QLabel(self.tab)
        self.label_23.setGeometry(QtCore.QRect(340, 250, 47, 13))
        self.label_23.setObjectName(_fromUtf8("label_23"))
        self.label_24 = QtGui.QLabel(self.tab)
        self.label_24.setGeometry(QtCore.QRect(80, 250, 47, 13))
        self.label_24.setObjectName(_fromUtf8("label_24"))
        self.label_25 = QtGui.QLabel(self.tab)
        self.label_25.setGeometry(QtCore.QRect(210, 250, 47, 13))
        self.label_25.setObjectName(_fromUtf8("label_25"))
        self.tabWidget.addTab(self.tab, _fromUtf8(""))
        self.tab_2 = QtGui.QWidget()
        self.tab_2.setObjectName(_fromUtf8("tab_2"))
        self.label_6 = QtGui.QLabel(self.tab_2)
        self.label_6.setGeometry(QtCore.QRect(300, 20, 47, 13))
        self.label_6.setObjectName(_fromUtf8("label_6"))
        self.listWidget_2 = QtGui.QListWidget(self.tab_2)
        self.listWidget_2.setGeometry(QtCore.QRect(510, 130, 256, 271))
        self.listWidget_2.setObjectName(_fromUtf8("listWidget_2"))
        self.label_7 = QtGui.QLabel(self.tab_2)
        self.label_7.setGeometry(QtCore.QRect(510, 90, 91, 16))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.label_7.setFont(font)
        self.label_7.setObjectName(_fromUtf8("label_7"))
        self.label_8 = QtGui.QLabel(self.tab_2)
        self.label_8.setGeometry(QtCore.QRect(40, 130, 47, 13))
        self.label_8.setObjectName(_fromUtf8("label_8"))
        self.lineEdit = QtGui.QLineEdit(self.tab_2)
        self.lineEdit.setGeometry(QtCore.QRect(100, 130, 151, 20))
        self.lineEdit.setObjectName(_fromUtf8("lineEdit"))
        self.lineEdit_2 = QtGui.QLineEdit(self.tab_2)
        self.lineEdit_2.setGeometry(QtCore.QRect(100, 190, 151, 20))
        self.lineEdit_2.setObjectName(_fromUtf8("lineEdit_2"))
        self.label_9 = QtGui.QLabel(self.tab_2)
        self.label_9.setGeometry(QtCore.QRect(40, 190, 47, 13))
        self.label_9.setObjectName(_fromUtf8("label_9"))
        self.lineEdit_3 = QtGui.QLineEdit(self.tab_2)
        self.lineEdit_3.setGeometry(QtCore.QRect(100, 250, 151, 20))
        self.lineEdit_3.setObjectName(_fromUtf8("lineEdit_3"))
        self.label_10 = QtGui.QLabel(self.tab_2)
        self.label_10.setGeometry(QtCore.QRect(40, 250, 47, 13))
        self.label_10.setObjectName(_fromUtf8("label_10"))
        self.lineEdit_4 = QtGui.QLineEdit(self.tab_2)
        self.lineEdit_4.setGeometry(QtCore.QRect(100, 310, 151, 20))
        self.lineEdit_4.setObjectName(_fromUtf8("lineEdit_4"))
        self.label_11 = QtGui.QLabel(self.tab_2)
        self.label_11.setGeometry(QtCore.QRect(40, 310, 47, 13))
        self.label_11.setObjectName(_fromUtf8("label_11"))
        self.pushButton_14 = QtGui.QPushButton(self.tab_2)
        self.pushButton_14.setGeometry(QtCore.QRect(80, 400, 75, 23))
        self.pushButton_14.setObjectName(_fromUtf8("pushButton_14"))
        self.pushButton_15 = QtGui.QPushButton(self.tab_2)
        self.pushButton_15.setGeometry(QtCore.QRect(180, 400, 75, 23))
        self.pushButton_15.setObjectName(_fromUtf8("pushButton_15"))
        self.pushButton_16 = QtGui.QPushButton(self.tab_2)
        self.pushButton_16.setGeometry(QtCore.QRect(510, 430, 75, 23))
        self.pushButton_16.setObjectName(_fromUtf8("pushButton_16"))
        self.pushButton_17 = QtGui.QPushButton(self.tab_2)
        self.pushButton_17.setGeometry(QtCore.QRect(10, 470, 75, 23))
        self.pushButton_17.setObjectName(_fromUtf8("pushButton_17"))
        self.checkBox_3 = QtGui.QCheckBox(self.tab_2)
        self.checkBox_3.setGeometry(QtCore.QRect(70, 360, 121, 17))
        self.checkBox_3.setObjectName(_fromUtf8("checkBox_3"))
        self.checkBox_4 = QtGui.QCheckBox(self.tab_2)
        self.checkBox_4.setGeometry(QtCore.QRect(190, 360, 121, 17))
        self.checkBox_4.setObjectName(_fromUtf8("checkBox_4"))
        self.label_12 = QtGui.QLabel(self.tab_2)
        self.label_12.setGeometry(QtCore.QRect(100, 90, 91, 16))
        font = QtGui.QFont()
        font.setPointSize(10)
        font.setBold(True)
        font.setWeight(75)
        self.label_12.setFont(font)
        self.label_12.setObjectName(_fromUtf8("label_12"))
        self.tabWidget.addTab(self.tab_2, _fromUtf8(""))
        self.tab_5 = QtGui.QWidget()
        self.tab_5.setObjectName(_fromUtf8("tab_5"))
        self.label_13 = QtGui.QLabel(self.tab_5)
        self.label_13.setGeometry(QtCore.QRect(390, 40, 47, 13))
        self.label_13.setObjectName(_fromUtf8("label_13"))
        self.label_14 = QtGui.QLabel(self.tab_5)
        self.label_14.setGeometry(QtCore.QRect(150, 250, 111, 16))
        self.label_14.setObjectName(_fromUtf8("label_14"))
        self.lineEdit_5 = QtGui.QLineEdit(self.tab_5)
        self.lineEdit_5.setGeometry(QtCore.QRect(270, 250, 351, 20))
        self.lineEdit_5.setObjectName(_fromUtf8("lineEdit_5"))
        self.label_15 = QtGui.QLabel(self.tab_5)
        self.label_15.setGeometry(QtCore.QRect(150, 290, 111, 16))
        self.label_15.setObjectName(_fromUtf8("label_15"))
        self.lineEdit_6 = QtGui.QLineEdit(self.tab_5)
        self.lineEdit_6.setGeometry(QtCore.QRect(270, 290, 351, 20))
        self.lineEdit_6.setObjectName(_fromUtf8("lineEdit_6"))
        self.label_16 = QtGui.QLabel(self.tab_5)
        self.label_16.setGeometry(QtCore.QRect(150, 340, 111, 16))
        self.label_16.setObjectName(_fromUtf8("label_16"))
        self.lineEdit_7 = QtGui.QLineEdit(self.tab_5)
        self.lineEdit_7.setGeometry(QtCore.QRect(270, 340, 351, 20))
        self.lineEdit_7.setObjectName(_fromUtf8("lineEdit_7"))
        self.pushButton_20 = QtGui.QPushButton(self.tab_5)
        self.pushButton_20.setGeometry(QtCore.QRect(10, 470, 75, 23))
        self.pushButton_20.setObjectName(_fromUtf8("pushButton_20"))
        self.pushButton_21 = QtGui.QPushButton(self.tab_5)
        self.pushButton_21.setGeometry(QtCore.QRect(250, 410, 91, 23))
        self.pushButton_21.setObjectName(_fromUtf8("pushButton_21"))
        self.pushButton_22 = QtGui.QPushButton(self.tab_5)
        self.pushButton_22.setGeometry(QtCore.QRect(374, 410, 91, 23))
        self.pushButton_22.setObjectName(_fromUtf8("pushButton_22"))
        self.pushButton_23 = QtGui.QPushButton(self.tab_5)
        self.pushButton_23.setGeometry(QtCore.QRect(490, 410, 91, 23))
        self.pushButton_23.setObjectName(_fromUtf8("pushButton_23"))
        self.pushButton_24 = QtGui.QPushButton(self.tab_5)
        self.pushButton_24.setGeometry(QtCore.QRect(600, 410, 91, 23))
        self.pushButton_24.setObjectName(_fromUtf8("pushButton_24"))
        self.lineEdit_9 = QtGui.QLineEdit(self.tab_5)
        self.lineEdit_9.setGeometry(QtCore.QRect(270, 200, 51, 20))
        self.lineEdit_9.setText(_fromUtf8(""))
        self.lineEdit_9.setObjectName(_fromUtf8("lineEdit_9"))
        self.label_27 = QtGui.QLabel(self.tab_5)
        self.label_27.setGeometry(QtCore.QRect(120, 200, 141, 16))
        self.label_27.setObjectName(_fromUtf8("label_27"))
        self.tabWidget.addTab(self.tab_5, _fromUtf8(""))
        self.tab_3 = QtGui.QWidget()
        self.tab_3.setObjectName(_fromUtf8("tab_3"))
        self.pushButton_19 = QtGui.QPushButton(self.tab_3)
        self.pushButton_19.setGeometry(QtCore.QRect(10, 470, 75, 23))
        self.pushButton_19.setObjectName(_fromUtf8("pushButton_19"))
        self.label_26 = QtGui.QLabel(self.tab_3)
        self.label_26.setGeometry(QtCore.QRect(210, 60, 371, 81))
        font = QtGui.QFont()
        font.setFamily(_fromUtf8("MS Serif"))
        font.setPointSize(24)
        font.setBold(True)
        font.setWeight(75)
        self.label_26.setFont(font)
        self.label_26.setObjectName(_fromUtf8("label_26"))
        self.label_28 = QtGui.QLabel(self.tab_3)
        self.label_28.setGeometry(QtCore.QRect(40, 180, 761, 20))
        self.label_28.setObjectName(_fromUtf8("label_28"))
        self.label_29 = QtGui.QLabel(self.tab_3)
        self.label_29.setGeometry(QtCore.QRect(280, 220, 291, 20))
        self.label_29.setObjectName(_fromUtf8("label_29"))
        self.label_30 = QtGui.QLabel(self.tab_3)
        self.label_30.setGeometry(QtCore.QRect(40, 270, 761, 20))
        self.label_30.setObjectName(_fromUtf8("label_30"))
        self.tabWidget.addTab(self.tab_3, _fromUtf8(""))
        self.label_13 = QtGui.QLabel(self.tab_5)
        self.label_13.setGeometry(QtCore.QRect(390, 5, 200, 200))
        self.label_13.setObjectName(_fromUtf8("label_13"))
        self.label_6 = QtGui.QLabel(self.tab_2)
        self.label_6.setGeometry(QtCore.QRect(300, 20, 180, 180))
        self.label_6.setObjectName(_fromUtf8("label_6"))
        self.label_6.setPixmap(QtGui.QPixmap(os.getcwd()+ "/user.bmp"))
        self.label_13.setPixmap(QtGui.QPixmap(os.getcwd()+ "/sett.png"))
        self.horizontalSlider.setMinimum(0)
        self.horizontalSlider.setMaximum(2)
        self.horizontalSlider.setTickInterval(1)
        self.horizontalSlider_2.setMinimum(0)
        self.horizontalSlider_2.setMaximum(2)
        self.horizontalSlider_2.setTickInterval(1)
        self.horizontalSlider_3.setMinimum(0)
        self.horizontalSlider_3.setMaximum(2)
        self.horizontalSlider_3.setTickInterval(1)
        self.pushButton_13.clicked.connect(quit)
        self.pushButton_17.clicked.connect(quit)
        #self.pushButton_18.clicked.connect(quit)
        self.pushButton_19.clicked.connect(quit)
        self.pushButton_5.clicked.connect(czip.move)
        self.pushButton_4.clicked.connect(czip.delete)
        self.pushButton_16.clicked.connect(ac.delete)
        self.pushButton.clicked.connect(fO.start)
        self.pushButton_14.clicked.connect(ac.create)
        self.pushButton_21.clicked.connect(tool.save)
        self.pushButton_23.clicked.connect(tool.restoreDefaults)
        self.checkBox_2.clicked.connect(fO.status)
        self.horizontalSlider.sliderMoved.connect(fO.noChange)
        self.horizontalSlider_2.sliderMoved.connect(fO.noChange)
        self.horizontalSlider_2.sliderMoved.connect(fO.noChange)
        """
        self.pushButton_8.clicked.connect(feedObj.playback)"""
        self.pushButton_3.clicked.connect(fO.stop)
        self.pushButton_9.clicked.connect(fO.playBack)
        self.pushButton_2.clicked.connect(fO.watch)
        #feedObj.feel()
        ac.setUp()
        fO.feel()
        self.retranslateUi(Dialog)
        self.tabWidget.setCurrentIndex(0)
        QtCore.QMetaObject.connectSlotsByName(Dialog)

    def retranslateUi(self, Dialog):
        Dialog.setWindowTitle(_translate("Dialog", "Control Panel", None))
        self.pushButton_9.setText(_translate("Dialog", "Play", None))
        self.label.setText(_translate("Dialog", "Face Detaction", None))
        self.label_4.setText(_translate("Dialog", "RECORDED FOOTAGE", None))
        self.label_3.setText(_translate("Dialog", "Motion Detaction", None))
        self.pushButton_2.setText(_translate("Dialog", "Watch Feed", None))
        self.pushButton_3.setText(_translate("Dialog", "Stop", None))
        self.pushButton_13.setText(_translate("Dialog", "Exit", None))
        self.pushButton_4.setText(_translate("Dialog", "Delete", None))
        self.pushButton_5.setText(_translate("Dialog", "Compress", None))
        self.label_5.setText(_translate("Dialog", "Notifications", None))
        self.pushButton_8.setText(_translate("Dialog", "Snapshot", None))
        self.pushButton_12.setText(_translate("Dialog", "Clear", None))
        self.pushButton.setText(_translate("Dialog", "Start", None))
        self.label_2.setText(_translate("Dialog", "Body Detaction", None))
        self.checkBox.setText(_translate("Dialog", "Use Custom Object", None))
        self.checkBox_2.setText(_translate("Dialog", "Use Recorded Footage", None))
        self.label_17.setText(_translate("Dialog", "Off", None))
        self.label_18.setText(_translate("Dialog", "normal", None))
        self.label_19.setText(_translate("Dialog", "Aggresive", None))
        self.label_20.setText(_translate("Dialog", "Aggresive", None))
        self.label_21.setText(_translate("Dialog", "Off", None))
        self.label_22.setText(_translate("Dialog", "normal", None))
        self.label_23.setText(_translate("Dialog", "Aggresive", None))
        self.label_24.setText(_translate("Dialog", "Off", None))
        self.label_25.setText(_translate("Dialog", "normal", None))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab), _translate("Dialog", "Feed Control", None))
        #self.label_6.setText(_translate("Dialog", "TextLabel", None))
        self.label_7.setText(_translate("Dialog", "Current Users", None))
        self.label_8.setText(_translate("Dialog", "Username", None))
        self.label_9.setText(_translate("Dialog", "Email", None))
        self.label_10.setText(_translate("Dialog", "Password", None))
        self.label_11.setText(_translate("Dialog", "Password", None))
        self.pushButton_14.setText(_translate("Dialog", "Create", None))
        self.pushButton_15.setText(_translate("Dialog", "Cancel", None))
        self.pushButton_16.setText(_translate("Dialog", "DELETE", None))
        self.pushButton_17.setText(_translate("Dialog", "Exit", None))
        self.checkBox_3.setText(_translate("Dialog", "Admin", None))
        self.checkBox_4.setText(_translate("Dialog", "User", None))
        self.label_12.setText(_translate("Dialog", "Create Users", None))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab_2), _translate("Dialog", "Account Control", None))
        #self.label_13.setText(_translate("Dialog", "TextLabel", None))
        self.label_14.setText(_translate("Dialog", "Video Save Directory", None))
        self.label_15.setText(_translate("Dialog", "Custom Object(Haar)", None))
        self.label_16.setText(_translate("Dialog", "Recorded Footage", None))
        self.pushButton_20.setText(_translate("Dialog", "Exit", None))
        self.pushButton_21.setText(_translate("Dialog", "Save", None))
        self.pushButton_22.setText(_translate("Dialog", "Apply", None))
        self.pushButton_23.setText(_translate("Dialog", "Restore Defaults", None))
        self.pushButton_24.setText(_translate("Dialog", "Cancel", None))
        self.label_27.setText(_translate("Dialog", "Video Duration( seconds)", None))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab_5), _translate("Dialog", "Settings", None))
        self.pushButton_19.setText(_translate("Dialog", "Exit", None))
        self.label_26.setText(_translate("Dialog", "Author:Samuel Jarai", None))
        self.label_28.setText(_translate("Dialog", "I am a secoond year software engineering student at the Harare Institute of Technology and I made this surveillence system as a proof of concept", None))
        self.label_29.setText(_translate("Dialog", "that is,it is not meant for practical use.", None))
        self.label_30.setText(_translate("Dialog", "Technologies used. Python 3.x , Open CV 3.x.x  ,PyQt4.", None))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab_3), _translate("Dialog", "About", None))


if __name__ == "__main__":
    import sys
    n=notifications()
    t=threading.Thread(target=n.login)
    t.start()
    #n.login()
    
    tool=Settings()
    tool.setUp()
    tool.retrieve()
    ac=accounts()
    
    czip=compression()
    fO=new_Feed()
    fO.setUpVars()
    
    #feedObj=Feed()
    #feedObj.setupVars()
    app = QtGui.QApplication(sys.argv)
    Dialog = QtGui.QDialog()
    ui = Ui_Dialog()
    ui.setupUi(Dialog)
    Dialog.show()
    sys.exit(app.exec_())

