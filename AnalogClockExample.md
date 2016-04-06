# Analog Clock Example #
![http://ekarchive.elikirk.com/david/clock.jpg](http://ekarchive.elikirk.com/david/clock.jpg)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using com.trolltech.qt.core;
using com.trolltech.qt.gui;

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

public class AnalogClock : QWidget
{
    static QPolygon hourHand = new QPolygon();
    static QPolygon minuteHand = new QPolygon();
    
    QTimer m_timer = new QTimer();

    public AnalogClock(QWidget parent) :base(parent) {
        hourHand.append(new QPoint(7, 8));
        hourHand.append(new QPoint(-7, 8));
        hourHand.append(new QPoint(0, -40));

        minuteHand.append(new QPoint(7, 8));
        minuteHand.append(new QPoint(-7, 8));
        minuteHand.append(new QPoint(0, -70));

        m_timer.timeout.connect(this, "update()");

        setWindowTitle("Analog clock");
        setWindowIcon(new QIcon("classpath:com/trolltech/images/qt-logo.png"));
        resize(200, 200);
    }

    
    protected override void paintEvent(QPaintEvent e)
    {
        QColor hourColor = new QColor(127, 0, 127);
        QColor minuteColor = new QColor(0, 127, 127, 191);

        int side = width() < height() ? width() : height();

        QTime time = QTime.currentTime();

        QPainter painter = new QPainter(this);
        painter.setRenderHint(QPainter.RenderHint.Antialiasing);
        painter.translate(width() / 2, height() / 2);
        painter.scale(side / 200.0f, side / 200.0f);

        painter.setPen(QPen.NoPen);
        painter.setBrush(hourColor);

        painter.save();
        painter.rotate(30.0f * ((time.hour() + time.minute() / 60.0f)));
        painter.drawConvexPolygon(hourHand);
        painter.restore();

        painter.setPen(hourColor);

        for (int i=0; i<12; ++i) {
            painter.drawLine(88, 0, 96, 0);
            painter.rotate(30.0f);
        }

        painter.setPen(QPen.NoPen);
        painter.setBrush(minuteColor);

        painter.save();
        painter.rotate(6.0f * (time.minute() + time.second() / 60.0f));
        painter.drawConvexPolygon(minuteHand);
        painter.restore();

        painter.setPen(minuteColor);

        for (int j=0; j<60; ++j) {
            if ((j % 5) != 0)
                painter.drawLine(92, 0, 96, 0);
            painter.rotate(6.0f);
        }
    }

    
    public override QSize sizeHint() {
        return new QSize(200, 200);
    }

    
    protected override void showEvent(QShowEvent e) {
        m_timer.start(1000);
    }

    
    protected override void hideEvent(QHideEvent e) {
        m_timer.stop();
    }

    static public void Main(string[] args)
    {
        QApplication.initialize(args);
        AnalogClock w = new AnalogClock(null);

        w.show();

        QApplication.exec();
    }
}
```