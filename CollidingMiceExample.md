Colliding Mice Example:
![http://ekarchive.elikirk.com/david/collidingmice.jpg](http://ekarchive.elikirk.com/david/collidingmice.jpg)
```
using System.Collections.Generic;
using System.Linq;
using System.Text;    
using com.trolltech.qt.core;
using com.trolltech.qt.gui;
using com.trolltech.qt.webkit;
using System;
using System.Resources;
using System.Reflection;
namespace Test
{
    public class CollidingMice
    {
        public event EventHandler<EventArgs> start;
        public event EventHandler<EventArgs> stop;
        public QWidget widget { get; set; }
        private const int MouseCount = 7;
        public CollidingMice()
        {
            this.widget = new QWidget();
            QGraphicsScene scene = new QGraphicsScene(this.widget);
            scene.setSceneRect(-300, -300, 600, 600);
            scene.setItemIndexMethod(QGraphicsScene.ItemIndexMethod.NoIndex);

            for (int i = 0; i < MouseCount; ++i)
            {
                Mouse mouse = new Mouse(this);
                mouse.setPos(Math.Sin((i * 6.28) / MouseCount) * 200,
                             Math.Cos((i * 6.28) / MouseCount) * 200);
                scene.addItem(mouse);
            }
            QGraphicsView view = new QGraphicsView(scene);
            view.setRenderHint(QPainter.RenderHint.Antialiasing);

            view.setBackgroundBrush(new QBrush(new QPixmap("cheese.png")));
            view.setCacheMode(new QGraphicsView.CacheMode(QGraphicsView.CacheModeFlag.CacheBackground));
            view.setDragMode(QGraphicsView.DragMode.ScrollHandDrag);
            view.setViewportUpdateMode(QGraphicsView.ViewportUpdateMode.FullViewportUpdate);

            QGridLayout layout = new QGridLayout();
            layout.addWidget(view, 0, 0);
            this.widget.setLayout(layout);

            this.widget.setWindowTitle("Colliding Mice");
            this.widget.setWindowIcon(new QIcon("Icon.png"));
            this.widget.resize(400, 300);

        }
        public class Mouse : QGraphicsItem
        {
            double angle = 0;
            double speed = 0;
            double mouseEyeDirection = 0;
            QColor color = null;
            Random generator = new Random();
            const double TWO_PI = Math.PI * 2;
            private QTimer timer;
            public Mouse(CollidingMice parent)
            {
                color = new QColor(generator.Next(256), generator.Next(256), generator.Next(256));
                rotate(generator.NextDouble() * 360);
                timer = new QTimer(parent.widget);
                timer.start(1000/33);
                timer.timeout.connect(this, "move()");
                parent.start += new EventHandler<EventArgs>(parent_start);
                parent.stop += new EventHandler<EventArgs>(parent_stop);
                _boundingRect = new QRectF(-20 - adjust, -22 - adjust, 40 + adjust, 83 + adjust);
                _shape = new QPainterPath();
                _shape.addRect(-10, -20, 20, 40);
                _tail = new QPainterPath(new QPointF(0, 20));
                _tail.cubicTo(-5, 22, -5, 22, 0, 25);
                _tail.cubicTo(5, 27, 5, 32, 0, 30);
                _tail.cubicTo(-5, 32, -5, 42, 0, 35);
                pupilRect1 = new QRectF(-8 + mouseEyeDirection, -17, 4, 4);
                pupilRect2 = new QRectF(4 + mouseEyeDirection, -17, 4, 4);
            }

            void parent_stop(object sender, EventArgs e)
            {
                timer.stop();
            }

            void parent_start(object sender, EventArgs e)
            {
                timer.start();
            }
            private double adjust = 0.5;
            private QRectF _boundingRect;
            public override QRectF boundingRect() {
                return _boundingRect;
            }
            private QPainterPath _shape;    
            public override QPainterPath shape() {
                return _shape;
            }
            QBrush brush = new QBrush(Qt.BrushStyle.SolidPattern);
            QPainterPath _tail;
            private QRectF pupilRect1;
            private QRectF pupilRect2; 

            public override void paint(QPainter painter,
                          QStyleOptionGraphicsItem styleOptionGraphicsItem,
                          QWidget widget) {

                // Body
                painter.setBrush(color);
                painter.drawEllipse(-10, -20, 20, 40);

                // Eyes
                brush.setColor(QColor.white);
                painter.setBrush(brush);
                painter.drawEllipse(-10, -17, 8, 8);
                painter.drawEllipse(2, -17, 8, 8);

                // Nose
                brush.setColor(QColor.darkBlue);
                painter.setBrush(brush);
                painter.drawEllipse(-2, -22, 4, 4);

                // Pupils
                painter.drawEllipse(pupilRect1);
                painter.drawEllipse(pupilRect2);

                // Ears
                if (scene().collidingItems(this).isEmpty())
                    brush.setColor(QColor.darkYellow);
                else
                    brush.setColor(QColor.darkRed);
                painter.setBrush(brush);

                painter.drawEllipse(-17, -12, 16, 16);
                painter.drawEllipse(1, -12, 16, 16);

                // Tail
                painter.setBrush(QBrush.NoBrush);
                painter.drawPath(_tail);
            }

            private QPolygonF polygon = new QPolygonF();
            private QPointF origo = new QPointF(0, 0);
            public void move() {
                // Don't move too far away
                QLineF lineToCenter = new QLineF(origo, mapFromScene(0, 0));
                if (lineToCenter.length() > 150) 
                {
                    double angleToCenter = Math.Acos(lineToCenter.dx() / lineToCenter.length());
                    if (lineToCenter.dy() < 0){
                        angleToCenter = TWO_PI - angleToCenter;
                    }
                    angleToCenter = normalizeAngle((Math.PI - angleToCenter) + Math.PI / 2);

                    if (angleToCenter < Math.PI && angleToCenter > Math.PI / 4) {
                        // Rotate left
                        angle += (angle < -Math.PI / 2) ? 0.25 : -0.25;
                    } else if (angleToCenter >= Math.PI
                               && angleToCenter < (Math.PI + Math.PI / 2
                                                   + Math.PI / 4)) {
                        // Rotate right
                        angle += (angle < Math.PI / 2) ? 0.25 : -0.25;
                    }
                } else if (Math.Sin(angle) < 0) {
                    angle += 0.25;
                } else if (Math.Sin(angle) > 0) {
                    angle -= 0.25;
                }

                // Try not to crash with any other mice

                polygon.clear();
                polygon.append(mapToScene(0, 0));
                polygon.append(mapToScene(-30, -50));
                polygon.append(mapToScene(30, -50));

                java.util.List dangerMice = scene().items(polygon);
                for (int m = 0; m < dangerMice.size(); m++) {
                    QGraphicsItemInterface item = (QGraphicsItemInterface)dangerMice.get(m);
                    if (item == this)
                        continue;

                    QLineF lineToMouse = new QLineF(origo, mapFromItem(item, 0, 0));
                    double angleToMouse = Math.Acos(lineToMouse.dx() / lineToMouse.length());
                    if (lineToMouse.dy() < 0)
                        angleToMouse = TWO_PI - angleToMouse;
                    angleToMouse = normalizeAngle((Math.PI - angleToMouse) + Math.PI / 2);

                    if (angleToMouse >= 0 && angleToMouse < (Math.PI / 2)) {
                        // Rotate right
                        angle += 0.5;
                    } else if (angleToMouse <= TWO_PI && angleToMouse > (TWO_PI - Math.PI / 2)) {
                        // Rotate left
                        angle -= 0.5;
                    }
                }

                // Add some random movement
                if (dangerMice.size() < 1 && generator.NextDouble() < 0.1) {
                    if (generator.NextDouble() > 0.5)
                        angle += generator.NextDouble() / 5;
                    else
                        angle -= generator.NextDouble() / 5;
                }

                speed += (-50 + generator.NextDouble() * 100) / 100.0;

                double dx = Math.Sin(angle) * 10;
                mouseEyeDirection = (Math.Abs(dx / 5) < 1) ? 0 : dx / 5;

                rotate(dx);
                setPos(mapToParent(0, -(3 + Math.Sin(speed) * 3)));
            }

            private double normalizeAngle(double angle) {
                while (angle < 0)
                    angle += TWO_PI;
                while (angle > TWO_PI)
                    angle -= TWO_PI;
                return angle;
            }
        }
        private void Show()
        {
            this.widget.show();
        }
        public static void Main(string[] args)
        {
            QApplication.initialize(args);
            CollidingMice collidingMice = new CollidingMice();
            collidingMice.Show();
            QApplication.exec();
        }        
    }
}
```