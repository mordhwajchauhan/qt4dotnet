
```
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

using com.trolltech.qt.core;
using com.trolltech.qt.gui;
using com.trolltech.qt.opengl;

public class  Painting2D : QWidget
{
    private Helper helper;

    public Painting2D() {
        helper = new Helper();

        Widget widget = new Widget(helper, this);
        GLWidget openGL = new GLWidget(helper, this);
        QLabel nativeLabel = new QLabel(tr("Native"));
        nativeLabel.setAlignment(Qt.AlignmentFlag.AlignHCenter);
        QLabel openGLLabel = new QLabel(tr("OpenGL"));
        openGLLabel.setAlignment(Qt.AlignmentFlag.AlignHCenter);

        QGridLayout layout = new QGridLayout();
        layout.addWidget(widget, 0, 0);
        layout.addWidget(openGL, 0, 1);
        layout.addWidget(nativeLabel, 1, 0);
        layout.addWidget(openGLLabel, 1, 1);
        setLayout(layout);

        QTimer timer = new QTimer(this);
        timer.timeout.connect(openGL, "animate()");
        timer.timeout.connect(widget, "animate()");
        timer.setInterval(1);
        timer.start(50);

        setWindowTitle(tr("2D Painting on Native and OpenGL Widgets"));
        setWindowIcon(new QIcon("classpath:com/trolltech/images/qt-logo.png"));
    }

    class GLWidget : QGLWidget {
        private Helper helper;
        private int elapsed;

        public GLWidget(Helper helper, QWidget parent):base(new QGLFormat(),parent) {
            this.helper = helper;
            setFixedSize(200, 200);
        }

        public void animate() {
            elapsed = (elapsed + ((QTimer) signalSender()).interval()) % 1000;
            repaint();
        }

        protected override void paintEvent(QPaintEvent evt) {
            QPainter painter = new QPainter();
            painter.begin(this);
            painter.setRenderHint(QPainter.RenderHint.Antialiasing);
            helper.paint(painter, evt, elapsed);
            painter.end();
        }
    }

    class Widget : QWidget {
        private int elapsed;
        private Helper helper;

        public Widget(Helper helper, QWidget parent) : base(parent) {
            this.helper = helper;
            setFixedSize(200, 200);
        }

        public void animate() {
            elapsed = (elapsed + ((QTimer) signalSender()).interval()) % 1000;
            repaint();
        }

        protected override void paintEvent(QPaintEvent evt) {
            QPainter painter = new QPainter();
            painter.begin(this);
            painter.setRenderHint(QPainter.RenderHint.Antialiasing);
            helper.paint(painter, evt, elapsed);
            painter.end();
        }
    }

    class Helper {
        private QBrush background;
        private QBrush circleBrush;
        private QFont textFont;
        private QPen circlePen;
        private QPen textPen;

        public Helper() {
            QLinearGradient gradient =
                new QLinearGradient(new QPointF(50, -20), new QPointF(80, 20));
            gradient.setColorAt(0.0, new QColor(Qt.GlobalColor.white));
            gradient.setColorAt(1.0, new QColor(0xa6, 0xce, 0x39));

            background = new QBrush(new QColor(64, 32, 64));
            circleBrush = new QBrush(gradient);
            circlePen = new QPen(new QColor(Qt.GlobalColor.black));
            circlePen.setWidth(1);
            textPen = new QPen(new QColor(Qt.GlobalColor.white));

            textFont = new QFont();
            textFont.setPixelSize(50);
        }

        public void paint(QPainter painter, QPaintEvent evt, int elapsed) {
            painter.fillRect(evt.rect(), background);
            painter.translate(100, 100);

            painter.save();
            painter.setBrush(circleBrush);
            painter.setPen(circlePen);
            painter.rotate(elapsed * 0.030);

            double r = (elapsed)/1000.0;
            int n = 30;
            for (int i = 0; i < n; ++i) {
                painter.rotate(30);
                double radius = 0 + 120.0*((i+r)/n);
                double circleRadius = 1 + ((i+r)/n)*20;
                painter.drawEllipse(new QRectF(radius, -circleRadius,
                circleRadius*2, circleRadius*2));
            }
            painter.restore();

            painter.setPen(textPen);
            painter.setFont(textFont);
            painter.drawText(new QRect(-50, -50, 100, 100), Qt.AlignmentFlag.AlignCenter.value(), "Qt");
        }
    }

    public static void Main(string[] args) {
        QApplication.initialize(args);

        Painting2D painting2d = new Painting2D();
        painting2d.show();

        QApplication.exec();
    }
}
```