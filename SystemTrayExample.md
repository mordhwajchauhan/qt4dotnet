# System Tray Example #

![http://ekarchive.elikirk.com/david/systemtray.png](http://ekarchive.elikirk.com/david/systemtray.png)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using com.trolltech.qt.core;
using com.trolltech.qt.gui;
using com.trolltech.qt;
using java.util;

/****************************************************************************
 **
 ** Copyright (C) 1992-2008 Nokia Corporation and/or its subsidiary(-ies).
** All rights reserved.
 **
 ** This file is part of Qt Jambi.
 **
 ** 
** Commercial Usage
** Licensees holding valid Qt Commercial licenses may use this file in
** accordance with the Qt Commercial License Agreement provided with the
** Software or, alternatively, in accordance with the terms contained in
** a written agreement between you and Nokia.
**
**
** GNU General Public License Usage
** Alternatively, this file may be used under the terms of the GNU
** General Public License versions 2.0 or 3.0 as published by the Free
** Software Foundation and appearing in the file LICENSE.GPL included in
** the packaging of this file.  Please review the following information
** to ensure GNU General Public Licensing requirements will be met:
** http://www.fsf.org/licensing/licenses/info/GPLv2.html and
** http://www.gnu.org/copyleft/gpl.html.  In addition, as a special
** exception, Nokia gives you certain additional rights. These rights
** are described in the Nokia Qt GPL Exception version 1.2, included in
** the file GPL_EXCEPTION.txt in this package.
** 
** Qt for Windows(R) Licensees
** As a special exception, Nokia, as the sole copyright holder for Qt
** Designer, grants users of the Qt/Eclipse Integration plug-in the
** right for the Qt/Eclipse Integration to link to functionality
** provided by Qt Designer and its related libraries.
**
**
** If you are unsure which license is appropriate for your use, please
** contact the sales department at qt-sales@nokia.com.
 **
 ** This file is provided AS IS with NO WARRANTY OF ANY KIND, INCLUDING THE
 ** WARRANTY OF DESIGN, MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.
 **
 ****************************************************************************/
public class SystemTrayExample : QWidget {

    private QSystemTrayIcon trayIcon;
    private QMenu trayIconMenu;

    private QLineEdit titleEdit;
    private QTextEdit messageEdit;
    private QComboBox typeCombo;

    private QTextEdit infoDisplay;
    private QComboBox iconCombo;

    private QAction toggleVisibilityAction;

    public static void Main(string[] args) {
        QApplication.initialize(args);

        SystemTrayExample editor = new SystemTrayExample(null);
        editor.show();

        QApplication.exec();
    }


    public SystemTrayExample(QWidget parent) : base(parent) {        
        if (!QSystemTrayIcon.isSystemTrayAvailable())
            QMessageBox.warning(this, tr("System tray is unavailable"),
                                      tr("System tray unavailable"));

        // Create the menu that will be used for the context menu
        trayIconMenu = new QMenu(this);
        trayIconMenu.aboutToShow.connect(this, "updateMenu()");

        toggleVisibilityAction = new QAction("Show/Hide", this);
        toggleVisibilityAction.triggered.connect(this, "toggleVisibility()");
        trayIconMenu.addAction(toggleVisibilityAction);

        QAction restoreAction = new QAction("Restore", this);
        restoreAction.triggered.connect(this, "showNormal()");
        trayIconMenu.addAction(restoreAction);

        QAction minimizeAction = new QAction("Minimize", this);
        minimizeAction.triggered.connect(this, "showMinimized()");
        trayIconMenu.addAction(minimizeAction);

        QAction maximizeAction = new QAction("Maximize", this);
        maximizeAction.triggered.connect(this, "showMaximized()");
        trayIconMenu.addAction(maximizeAction);

        trayIconMenu.addSeparator();

        QAction quitAction = new QAction("&Quit", this);
        quitAction.triggered.connect(this, "close()");
        trayIconMenu.addAction(quitAction);

        // Create the tray icon
        trayIcon = new QSystemTrayIcon(this);
        trayIcon.setToolTip("System trayIcon example");
        trayIcon.setContextMenu(trayIconMenu);

        trayIcon.activated.connect(this, "activated(com.trolltech.qt.gui.QSystemTrayIcon$ActivationReason)");
        trayIcon.messageClicked.connect(this, "balloonClicked()");

        changeIcon(0);
        trayIcon.show();

        QLabel titleLabel = new QLabel(tr("Message Title"));
        titleEdit = new QLineEdit(tr("Message Title"));

        QLabel messageLabel = new QLabel(tr("Message Contents"));
        messageEdit = new QTextEdit(tr("Man is more ape than many of the apes"));
        messageEdit.setAcceptRichText(false);

        QLabel typeLabel = new QLabel(tr("Message Type"));
        typeCombo = new QComboBox();
        Vector types = new Vector();
        types.add("NoIcon");
        types.add("Information");
        types.add("Warning");
        types.add("Critical");
        typeCombo.addItems(types);
        typeCombo.setCurrentIndex(2);

        QPushButton balloonButton = new QPushButton(tr("Balloon message"));
        balloonButton.setToolTip(tr("Click here to balloon the message"));
        balloonButton.clicked.connect(this, "showMessage()");

        infoDisplay = new QTextEdit(tr("Status messages will be visible here"));
        infoDisplay.setMaximumHeight(100);

        QCheckBox toggleIconCheckBox = new QCheckBox(tr("Show system tray icon"));
        toggleIconCheckBox.setChecked(true);
        toggleIconCheckBox.clicked.connect(trayIcon, "setVisible(boolean)");

        QLabel iconLabel = new QLabel("Select icon");
        iconCombo = new QComboBox();
        Vector icons = new Vector();
        icons.add("16x16 icon");
        icons.add("22x22 icon");
        icons.add("32x32 icon");
        iconCombo.addItems(icons);
        iconCombo.activatedIndex.connect(this, "changeIcon(int)");

        QGridLayout layout = new QGridLayout();
        layout.addWidget(titleLabel, 0, 0);
        layout.addWidget(titleEdit, 0, 1);
        layout.addWidget(messageLabel, 1, 0);
        layout.addWidget(messageEdit, 1, 1);
        layout.addWidget(typeLabel, 2, 0);
        layout.addWidget(typeCombo, 2, 1);
        layout.addWidget(balloonButton, 4, 1);
        layout.addWidget(infoDisplay, 5, 0, 1, 2);
        layout.addWidget(toggleIconCheckBox, 6, 0);
        layout.addWidget(iconLabel, 7, 0);
        layout.addWidget(iconCombo, 7, 1);
        setLayout(layout);

        setWindowTitle(tr("System Tray Example"));
        setWindowIcon(new QIcon("qt-logo.png"));
    }

    protected override void closeEvent(QCloseEvent e) {

    }

    protected void updateMenu() {
        toggleVisibilityAction.setText(isVisible() ? tr("Hide") : tr("Show"));
    }

    protected void toggleVisibility() {
        if (isVisible())
            hide();
        else
            show();
    }

    protected void showMessage() {
        if (QSysInfo.macVersion() != 0) {
            QMessageBox.information(this, tr("System tray example"),
                    tr("Balloon tips are not supported on Mac OS X"));
        } else {
            QSystemTrayIcon.MessageIcon icon;
            icon = QSystemTrayIcon.MessageIcon.resolve(typeCombo.currentIndex());
			Console.WriteLine(icon);
            trayIcon.showMessage(titleEdit.text(), messageEdit.toPlainText(), icon, 10000);
            trayIcon.setToolTip(titleEdit.text());
        }
    }

    protected void balloonClicked() {
        infoDisplay.append(tr("Balloon message was clicked"));
    }

    public void activated(QSystemTrayIcon.ActivationReason reason) {
        String name = QSystemTrayIcon.MessageIcon.resolve(reason.value()).name();
        if (name != null)
            infoDisplay.append("Activated - Reason " + name);
    }

    protected void changeIcon(int index) {
        String iconName;
        switch (index) {
        default:
        case 0:
            iconName = "icon_16x16.png";
            break;

        case 1:
            iconName = "icon_22x22.png";
            break;

        case 2:
            iconName = "icon_32x32.png";
            break;
        }
        QPixmap pixmap = new QPixmap(iconName);
        trayIcon.setIcon(new QIcon(pixmap));
    }
}
```