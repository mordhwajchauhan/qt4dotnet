# Webkit Example #
Example using the webkit features
![http://ekarchive.elikirk.com/david/Webkit.jpg](http://ekarchive.elikirk.com/david/Webkit.jpg)

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using com.trolltech.qt.core;
using com.trolltech.qt.gui;
using com.trolltech.qt.webkit;

public class HelloWebKit : QMainWindow {

    private QWebView browser;
    private QLineEdit field;

    private QAction forward;
    private QAction backward;
    private QAction reload;
    private QAction stop;

    public HelloWebKit(QWidget parent) : base(parent) {

        field = new QLineEdit();
        field.setText("http://trolltech.com");
        browser = new QWebView();

        // Toolbar...
        QToolBar toolbar = addToolBar("Actions");
        backward = toolbar.addAction("Backward");
        forward = toolbar.addAction("Forward");
        reload = toolbar.addAction("Reload");
        stop = toolbar.addAction("Stop");
        toolbar.addWidget(field);
        toolbar.setFloatable(false);
        toolbar.setMovable(false);

        setCentralWidget(browser);
        statusBar().show();

        // Connections
        field.returnPressed.connect(this, "open()");

        browser.loadStarted.connect(this, "loadStarted()");
        browser.loadProgress.connect(this, "loadProgress(int)");
        browser.loadFinished.connect(this, "loadDone()");
        browser.urlChanged.connect(this, "urlChanged(QUrl)");

        forward.triggered.connect(browser, "forward()");
        backward.triggered.connect(browser, "back()");
        reload.triggered.connect(browser, "reload()");
        stop.triggered.connect(browser, "stop()");
        this.open();
    }

    public void urlChanged(QUrl url) {
        field.setText(url.toString());
    }

    public void loadStarted() {
        statusBar().showMessage("Starting to load: " + field.text());
    }

    public void loadDone() {
        statusBar().showMessage("Loading done...");
    }

    public void loadProgress(int x) {
        statusBar().showMessage("Loading: " + x + " %");
    }

    public void open() {
        String text = field.text();

        if (text.IndexOf("://") < 0)
            text = "http://" + text;

        browser.load(new QUrl(text));
    }

    protected override void closeEvent(QCloseEvent evt) {
        browser.loadProgress.disconnect(this);
        browser.loadFinished.disconnect(this);
    }

    public static void Main(string[] args) {
        QApplication.initialize(args);

        HelloWebKit widget = new HelloWebKit(null);
        widget.show();

        QApplication.exec();
    }
}
```