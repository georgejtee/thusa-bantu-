# -*- coding: utf-8 -*-

# Form implementation generated from reading ui file 'login.ui'
#
# Created by: PyQt4 UI code generator 4.11.4
#
# WARNING! All changes made in this file will be lost!

from PyQt4 import QtCore, QtGui
import sqlite3
import subprocess
import threading
try:
    _fromUtf8 = QtCore.QString.fromUtf8
except AttributeError:
    def _fromUtf8(s):
        return s

try:
    _encoding = QtGui.QApplication.UnicodeUTF8
    def _translate(context, text, disambig):
        return QtGui.QApplication.translate(context, text, disambig, _encoding)
except AttributeError:
    def _translate(context, text, disambig):
        return QtGui.QApplication.translate(context, text, disambig)
#subprocess.call("pythonw main.pyw",shell=False)
class db():
    def clear(self):
        ui.lineEdit.setText('')
        ui.lineEdit_2.setText('')
    def initiate(self):
        con=sqlite3.connect(r'main.db')
        cur=con.cursor()
        #cur.execute('Create table users(username text,email text,password text)')
        #cur.execute('Insert into users values("sam" ,"jaggerjack61@gmail.com","jack")')
        #con.commit()
        cur.execute('select username from users')
        self.data=cur.fetchall()

        cur.execute('select password from users')
        self.dat=cur.fetchall()
        #cur.execute('Create table admin(username text,email text,password text)')
        #cur.execute('Insert into admin values("tap" ,"moga@gmail.com","toor")')
        #con.commit()
        #cur.execute('Create table Table1(Username text,password text)')
        #cur.execute('Insert into Table1 values("sam" ,"nigro")')
        #con.commit()
        cur.execute('select username from admin')
        self.username=cur.fetchall()

        cur.execute('select password from admin')
        self.passwords=cur.fetchall()
        cur.execute('select * from admin')
        self.all=cur.fetchall()
        cur.execute('select * from users')
        self.al=cur.fetchall()
        con.close()
        #os.system('taskkill /im cmd.exe')     
    def success(self):
        subprocess.call("pythonw system.pyw",shell=False)
        

    def login(self):
        print(self.all)
        print(self.al)
        print(self.dat[0])
        print(self.passwords)
        x=0
        y=0
        for row in self.data:
            y=0
            for item in row:
                if(item==ui.lineEdit.text()):
                    
                    
                    if(self.dat[x][y]==ui.lineEdit_2.text()):
                        t1=None
                        t1=threading.Thread(target=self.success)
                        t1.daemon=True
                        t1.start()
                        quit()
                y=y+1
            x=x+1
        x=0
        y=0
        for row in self.username:
            y=0
            for item in row:
                if(item==ui.lineEdit.text()):
                    print("tasvika 1")
                    
                    if(self.passwords[x][y]==ui.lineEdit_2.text()):
                        print("tasvika")
                        t1=None
                        t1=threading.Thread(target=self.success)
                        t1.daemon=True
                        t1.start()
                        quit()
                y=y+1
            x=x+1
        

class Ui_Dialog(object):
    def setupUi(self, Dialog):
        Dialog.setObjectName(_fromUtf8("Dialog"))
        Dialog.resize(542, 412)
        self.label = QtGui.QLabel(Dialog)
        self.label.setGeometry(QtCore.QRect(150, 40, 281, 31))
        font = QtGui.QFont()
        font.setPointSize(14)
        font.setBold(True)
        font.setWeight(75)
        self.label.setFont(font)
        self.label.setObjectName(_fromUtf8("label"))
        self.label_2 = QtGui.QLabel(Dialog)
        self.label_2.setGeometry(QtCore.QRect(60, 130, 71, 16))
        self.label_2.setObjectName(_fromUtf8("label_2"))
        self.lineEdit = QtGui.QLineEdit(Dialog)
        self.lineEdit.setGeometry(QtCore.QRect(170, 120, 231, 20))
        self.lineEdit.setObjectName(_fromUtf8("lineEdit"))
        self.label_3 = QtGui.QLabel(Dialog)
        self.label_3.setGeometry(QtCore.QRect(60, 230, 47, 13))
        self.label_3.setObjectName(_fromUtf8("label_3"))
        self.lineEdit_2 = QtGui.QLineEdit(Dialog)
        self.lineEdit_2.setGeometry(QtCore.QRect(170, 220, 231, 20))
        self.lineEdit_2.setObjectName(_fromUtf8("lineEdit_2"))
        self.pushButton = QtGui.QPushButton(Dialog)
        self.pushButton.setGeometry(QtCore.QRect(170, 300, 91, 23))
        self.pushButton.setObjectName(_fromUtf8("pushButton"))
        self.pushButton.clicked.connect(me.login)
        self.label_4 = QtGui.QLabel(Dialog)
        self.label_4.setGeometry(QtCore.QRect(250, 380, 81, 16))
        self.label_4.setObjectName(_fromUtf8("label_4"))
        self.pushButton_2 = QtGui.QPushButton(Dialog)
        self.pushButton_2.setGeometry(QtCore.QRect(314, 300, 91, 23))
        self.pushButton_2.setObjectName(_fromUtf8("pushButton_2"))
        self.pushButton_2.clicked.connect(db.clear)

        self.retranslateUi(Dialog)
        QtCore.QMetaObject.connectSlotsByName(Dialog)

    def retranslateUi(self, Dialog):
        Dialog.setWindowTitle(_translate("Dialog", "Login", None))
        self.label.setText(_translate("Dialog", "Computer Aided Surveillance", None))
        self.label_2.setText(_translate("Dialog", "User Name", None))
        self.label_3.setText(_translate("Dialog", "Password", None))
        self.pushButton.setText(_translate("Dialog", "Login", None))
        self.label_4.setText(_translate("Dialog", "Copyright 2017", None))
        self.pushButton_2.setText(_translate("Dialog", "Clear", None))


if __name__ == "__main__":
    import sys
    me=db()
    me.initiate()
    
    app = QtGui.QApplication(sys.argv)
    Dialog = QtGui.QDialog()
    ui = Ui_Dialog()
    ui.setupUi(Dialog)
    Dialog.show()
    sys.exit(app.exec_())

