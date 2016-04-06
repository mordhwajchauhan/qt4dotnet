# Tutorial 7 #
## One Thing Leads To Another ##

In this example we show how to mix .NET events and Signals and Slots. In qt4dotnet it is discouraged to use custom Signals. Instead we encourage to use events. Also we recommend not to derive from QWidget when using events. Instead create a widget and make it public and use it when needed. Please pay attention to the signal and slots in this example.

![http://ekarchive.elikirk.com/david/example7.png](http://ekarchive.elikirk.com/david/example7.png)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using com.trolltech.qt.core;
using com.trolltech.qt.gui;

public class ConnectedSliders
{
    public QWidget widget {get;set;} //We expose a widget property instead of deriving from QWidget
    public ConnectedSliders()
    {
	this.widget = new QWidget();
        QPushButton quit = new QPushButton(widget.tr("Quit"));
        quit.setFont(new QFont("Times", 18, QFont.Weight.Bold.value()));

        quit.clicked.connect(QApplication.instance(), "quit()");

        QGridLayout grid = new QGridLayout();
        LCDRange previousRange = null;

        for (int row = 0; row < 3; ++row) {
            for (int column = 0; column < 3; ++column) {
                LCDRange lcdRange = new LCDRange();
                grid.addWidget(lcdRange.rwidget, row, column);

            if (previousRange != null)
                lcdRange.valueChanged += new EventHandler<LCDRangeEventArgs>(previousRange.changeValue); //We use events instead of custom Signals
                previousRange = lcdRange;
            }
        }
        QVBoxLayout layout = new QVBoxLayout();
        layout.addWidget(quit);
        layout.addLayout(grid);
        widget.setLayout(layout);
        widget.setWindowTitle(widget.tr("One Thing Leads to Another"));
    }

    public class LCDRangeEventArgs : EventArgs{
        public int NewValue {get;set;}
    }

    class LCDRange 
    {
        private QSlider slider;
	public QWidget rwidget {get;set;}
	public event EventHandler<LCDRangeEventArgs> valueChanged;//We use events instead of custom Signals
	QLCDNumber lcd = new QLCDNumber(2);

	public void changeValue(object sender, LCDRangeEventArgs args){
	    lcd.display(args.NewValue);
	    slider.setValue(args.NewValue);
	    if (valueChanged !=null){
	        valueChanged(this,args);
	    }
	}

        //Slots work great! 
	private void changeValue(int newVal){
	    this.changeValue(null, new LCDRangeEventArgs(){ NewValue = newVal });
	}

        public LCDRange()
        {
            this.rwidget = new QWidget();
            lcd.setSegmentStyle(QLCDNumber.SegmentStyle.Filled);

            slider = new QSlider(Qt.Orientation.Horizontal);
            slider.setRange(0, 99);
            slider.setValue(0);

            //We call a slot when the valueChanged signal is fired. This slot will fire the event we created. We do this to avoid the creation of a custom signal. Custom signals are not supported by qt4dotnet.
            slider.valueChanged.connect(this, "changeValue(int)");

            QVBoxLayout layout = new QVBoxLayout();
            layout.addWidget(lcd);
            layout.addWidget(slider);
            rwidget.setLayout(layout);
        }
    }

    public void show(){
        widget.show();			
    }
	
    public static void Main(string[] args) {
        QApplication.initialize(args);
        ConnectedSliders widget = new ConnectedSliders();
    	widget.show();
    	QApplication.exec();
    }
}
```