# Calendar Example #

This was really easy to port from Qt Jambi. It took me 2 minutes. This example is running on KDE 4.2 with Mono 2.4 preview 3 and MacOS X 10.5 with Mono 2.4 preview 1

![http://ekarchive.elikirk.com/david/calendar.png](http://ekarchive.elikirk.com/david/calendar.png)
![http://ekarchive.elikirk.com/david/calendar-mac.png](http://ekarchive.elikirk.com/david/calendar-mac.png)


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

public class CalendarWidget : QWidget {
    private QGroupBox previewGroupBox;
    private QGridLayout previewLayout;
    private QCalendarWidget calendar;

    private QGroupBox generalOptionsGroupBox;
    private QLabel localeLabel;
    private QLabel firstDayLabel;
    private QLabel selectionModeLabel;
    private QLabel horizontalHeaderLabel;
    private QLabel verticalHeaderLabel;
    private QComboBox localeCombo;
    private QComboBox firstDayCombo;
    private QComboBox selectionModeCombo;
    private QCheckBox gridCheckBox;
    private QCheckBox navigationCheckBox;
    private QComboBox horizontalHeaderCombo;
    private QComboBox verticalHeaderCombo;

    private QGroupBox datesGroupBox;
    private QLabel currentDateLabel;
    private QLabel minimumDateLabel;
    private QLabel maximumDateLabel;
    private QDateEdit currentDateEdit;
    private QDateEdit minimumDateEdit;
    private QDateEdit maximumDateEdit;

    private QGroupBox textFormatsGroupBox;
    private QLabel weekdayColorLabel;
    private QLabel weekendColorLabel;
    private QLabel headerTextFormatLabel;
    private QComboBox weekdayColorCombo;
    private QComboBox weekendColorCombo;
    private QComboBox headerTextFormatCombo;

    private QCheckBox firstFridayCheckBox;
    private QCheckBox mayFirstCheckBox;

    public CalendarWidget() {
        createPreviewGroupBox();
        createGeneralOptionsGroupBox();
        createDatesGroupBox();
        createTextFormatsGroupBox();

        QGridLayout layout = new QGridLayout();
        layout.addWidget(previewGroupBox, 0, 0);
        layout.addWidget(generalOptionsGroupBox, 0, 1);
        layout.addWidget(datesGroupBox, 1, 0);
        layout.addWidget(textFormatsGroupBox, 1, 1);
        layout.setSizeConstraint(QLayout.SizeConstraint.SetFixedSize);
        setLayout(layout);

        previewLayout.setRowMinimumHeight(0, calendar.sizeHint().height());
        previewLayout.setColumnMinimumWidth(0, calendar.sizeHint().width());

        setWindowTitle(tr("Calendar Widget"));
    }

    private void localeChanged(int index) {
        calendar.setLocale((QLocale) localeCombo.itemData(index));
    }

    private void firstDayChanged(int index) {
        calendar.setFirstDayOfWeek((Qt.DayOfWeek) firstDayCombo.itemData(index));
    }

    private void selectionModeChanged(int index) {
        calendar.setSelectionMode((QCalendarWidget.SelectionMode) selectionModeCombo.itemData(index));
    }

    private void horizontalHeaderChanged(int index) {
        calendar.setHorizontalHeaderFormat((QCalendarWidget.HorizontalHeaderFormat)
            horizontalHeaderCombo.itemData(index));
    }

    private void verticalHeaderChanged(int index) {
        calendar.setVerticalHeaderFormat((QCalendarWidget.VerticalHeaderFormat)
            verticalHeaderCombo.itemData(index));
    }

    private void selectedDateChanged() {
        currentDateEdit.setDate(calendar.selectedDate());
    }

    private void minimumDateChanged(QDate date) {
        calendar.setMinimumDate(date);
        maximumDateEdit.setDate(calendar.maximumDate());
    }

    private void maximumDateChanged(QDate date) {
        calendar.setMaximumDate(date);
        minimumDateEdit.setDate(calendar.minimumDate());
    }

    private void weekdayFormatChanged() {
        QTextCharFormat format = new QTextCharFormat();

        format.setForeground(new QBrush((QColor) weekdayColorCombo.itemData(weekdayColorCombo.currentIndex())));
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Monday, format);
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Tuesday, format);
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Wednesday, format);
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Thursday, format);
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Friday, format);
    }

    private void weekendFormatChanged() {
        QTextCharFormat format = new QTextCharFormat();

        format.setForeground(new QBrush((QColor) weekendColorCombo.itemData(weekendColorCombo.currentIndex())));
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Saturday, format);
        calendar.setWeekdayTextFormat(Qt.DayOfWeek.Sunday, format);
    }

    private void reformatHeaders() {
        String text = headerTextFormatCombo.currentText();
        QTextCharFormat format = new QTextCharFormat();

        if (text.Equals(tr("Bold"))) {
            format.setFontWeight(QFont.Weight.Bold.value());
        } else if (text.Equals(tr("Italic"))) {
            format.setFontItalic(true);
        } else if (text.Equals(tr("Green"))) {
            format.setForeground(new QBrush(new QColor(Qt.GlobalColor.green)));
        }
        calendar.setHeaderTextFormat(format);
    }

    private void reformatCalendarPage() {
        QTextCharFormat mayFirstFormat = new QTextCharFormat();
        if (mayFirstCheckBox.isChecked())
            mayFirstFormat.setForeground(new QBrush(new QColor(Qt.GlobalColor.red)));

        QTextCharFormat firstFridayFormat = new QTextCharFormat();
        if (firstFridayCheckBox.isChecked())
            firstFridayFormat.setForeground(new QBrush(new QColor(Qt.GlobalColor.blue)));

        QDate date = new QDate(calendar.yearShown(), calendar.monthShown(), 1);

        calendar.setDateTextFormat(new QDate(date.year(), 5, 1), mayFirstFormat);

        date.setDate(date.year(), date.month(), 1);
        while (date.dayOfWeek() != Qt.DayOfWeek.Friday.value())
            date = date.addDays(1);
        calendar.setDateTextFormat(date, firstFridayFormat);
    }

    private void createPreviewGroupBox() {
        previewGroupBox = new QGroupBox(tr("Preview"));

        calendar = new QCalendarWidget();
        calendar.setMinimumDate(new QDate(1900, 1, 1));
        calendar.setMaximumDate(new QDate(3000, 1, 1));
        calendar.setGridVisible(true);

        calendar.currentPageChanged.connect(this, "reformatCalendarPage()");

        previewLayout = new QGridLayout();
        previewLayout.addWidget(calendar, 0, 0, Qt.AlignmentFlag.AlignCenter);
        previewGroupBox.setLayout(previewLayout);

    }

    private void createGeneralOptionsGroupBox() {
        generalOptionsGroupBox = new QGroupBox(tr("General Options"));

        localeCombo = new QComboBox();
        int curLocaleIndex = -1;
        int index = 0;
		foreach (var lang in QLocale.Language.values()) {
            var countries = QLocale.countriesForLanguage(lang);
            for (int i = 0; i < countries.size(); ++i) {
                QLocale.Country country = (QLocale.Country)countries.get(i);
                String label = QLocale.languageToString(lang);
                label += "/";
                label += QLocale.countryToString(country);
                QLocale locale = new QLocale(lang, country);
                if (this.locale().language() == lang && this.locale().country() == country)
                    curLocaleIndex = index;
                localeCombo.addItem(label, locale);
                ++index;
            }
        }
        if (curLocaleIndex != -1)
            localeCombo.setCurrentIndex(curLocaleIndex);
        localeLabel = new QLabel(tr("&Locale"));
        localeLabel.setBuddy(localeCombo);

        firstDayCombo = new QComboBox();
        firstDayCombo.addItem(tr("Sunday"), Qt.DayOfWeek.Sunday);
        firstDayCombo.addItem(tr("Monday"), Qt.DayOfWeek.Monday);
        firstDayCombo.addItem(tr("Tuesday"), Qt.DayOfWeek.Tuesday);
        firstDayCombo.addItem(tr("Wednesday"), Qt.DayOfWeek.Wednesday);
        firstDayCombo.addItem(tr("Thursday"), Qt.DayOfWeek.Thursday);
        firstDayCombo.addItem(tr("Friday"), Qt.DayOfWeek.Friday);
        firstDayCombo.addItem(tr("Saturday"), Qt.DayOfWeek.Saturday);

        firstDayLabel = new QLabel(tr("Wee&k starts on:"));
        firstDayLabel.setBuddy(firstDayCombo);

        selectionModeCombo = new QComboBox();
        selectionModeCombo.addItem(tr("Single selection"),
                                   QCalendarWidget.SelectionMode.SingleSelection);
        selectionModeCombo.addItem(tr("None"), QCalendarWidget.SelectionMode.NoSelection);

        selectionModeLabel = new QLabel(tr("&Selection mode:"));
        selectionModeLabel.setBuddy(selectionModeCombo);

        gridCheckBox = new QCheckBox(tr("&Grid"));
        gridCheckBox.setChecked(calendar.isGridVisible());

        navigationCheckBox = new QCheckBox(tr("&Navigation bar"));
        navigationCheckBox.setChecked(true);

        horizontalHeaderCombo = new QComboBox();
        horizontalHeaderCombo.addItem(tr("Single letter day names"),
                                       QCalendarWidget.HorizontalHeaderFormat.SingleLetterDayNames);
        horizontalHeaderCombo.addItem(tr("Short day names"),
                                       QCalendarWidget.HorizontalHeaderFormat.ShortDayNames);
        horizontalHeaderCombo.addItem(tr("None"),
                                       QCalendarWidget.HorizontalHeaderFormat.NoHorizontalHeader);
        horizontalHeaderCombo.setCurrentIndex(1);

        horizontalHeaderLabel = new QLabel(tr("&Horizontal header:"));
        horizontalHeaderLabel.setBuddy(horizontalHeaderCombo);

        verticalHeaderCombo = new QComboBox();
        verticalHeaderCombo.addItem(tr("ISO week numbers"),
                                     QCalendarWidget.VerticalHeaderFormat.ISOWeekNumbers);
        verticalHeaderCombo.addItem(tr("None"), QCalendarWidget.VerticalHeaderFormat.NoVerticalHeader);

        verticalHeaderLabel = new QLabel(tr("&Vertical header:"));
        verticalHeaderLabel.setBuddy(verticalHeaderCombo);

        localeCombo.currentIndexChanged.connect(this, "localeChanged(int)");
        firstDayCombo.currentIndexChanged.connect(this, "firstDayChanged(int)");
        selectionModeCombo.currentIndexChanged.connect(this, "selectionModeChanged(int)");
        gridCheckBox.toggled.connect(calendar, "setGridVisible(boolean)");
        navigationCheckBox.toggled.connect(calendar, "setNavigationBarVisible(boolean)");
        horizontalHeaderCombo.currentIndexChanged.connect(this, "horizontalHeaderChanged(int)");
        verticalHeaderCombo.currentIndexChanged.connect(this, "verticalHeaderChanged(int)");

        QHBoxLayout checkBoxLayout = new QHBoxLayout();
        checkBoxLayout.addWidget(gridCheckBox);
        checkBoxLayout.addStretch();
        checkBoxLayout.addWidget(navigationCheckBox);

        QGridLayout outerLayout = new QGridLayout();
        outerLayout.addWidget(localeLabel, 0, 0);
        outerLayout.addWidget(localeCombo, 0, 1);
        outerLayout.addWidget(firstDayLabel, 1, 0);
        outerLayout.addWidget(firstDayCombo, 1, 1);
        outerLayout.addWidget(selectionModeLabel, 2, 0);
        outerLayout.addWidget(selectionModeCombo, 2, 1);
        outerLayout.addLayout(checkBoxLayout, 3, 0, 1, 2);
        outerLayout.addWidget(horizontalHeaderLabel, 4, 0);
        outerLayout.addWidget(horizontalHeaderCombo, 4, 1);
        outerLayout.addWidget(verticalHeaderLabel, 5, 0);
        outerLayout.addWidget(verticalHeaderCombo, 5, 1);
        generalOptionsGroupBox.setLayout(outerLayout);

        firstDayChanged(firstDayCombo.currentIndex());
        selectionModeChanged(selectionModeCombo.currentIndex());
        horizontalHeaderChanged(horizontalHeaderCombo.currentIndex());
        verticalHeaderChanged(verticalHeaderCombo.currentIndex());
    }

    private void createDatesGroupBox() {
        datesGroupBox = new QGroupBox(tr("Dates"));

        minimumDateEdit = new QDateEdit();
        minimumDateEdit.setDisplayFormat("MMM d, yyyy");
        minimumDateEdit.setDateRange(calendar.minimumDate(),
                                      calendar.maximumDate());
        minimumDateEdit.setDate(calendar.minimumDate());


        minimumDateLabel = new QLabel(tr("&Minimum Date:"));
        minimumDateLabel.setBuddy(minimumDateEdit);

        currentDateEdit = new QDateEdit();
        currentDateEdit.setDisplayFormat("MMM d, yyyy");
        currentDateEdit.setDate(calendar.selectedDate());
        currentDateEdit.setDateRange(calendar.minimumDate(),
                                      calendar.maximumDate());

        currentDateLabel = new QLabel(tr("&Current Date:"));
        currentDateLabel.setBuddy(currentDateEdit);

        maximumDateEdit = new QDateEdit();
        maximumDateEdit.setDisplayFormat("MMM d, yyyy");
        maximumDateEdit.setDateRange(calendar.minimumDate(),
                                     calendar.maximumDate());
        maximumDateEdit.setDate(calendar.maximumDate());

        maximumDateLabel = new QLabel(tr("Ma&ximum Date:"));
        maximumDateLabel.setBuddy(maximumDateEdit);

        currentDateEdit.dateChanged.connect(calendar, "setSelectedDate(QDate)");
        calendar.selectionChanged.connect(this, "selectedDateChanged()");
        minimumDateEdit.dateChanged.connect(this, "minimumDateChanged(QDate)");
        maximumDateEdit.dateChanged.connect(this, "maximumDateChanged(QDate)");

        QGridLayout dateBoxLayout = new QGridLayout();
        dateBoxLayout.addWidget(currentDateLabel, 1, 0);
        dateBoxLayout.addWidget(currentDateEdit, 1, 1);
        dateBoxLayout.addWidget(minimumDateLabel, 0, 0);
        dateBoxLayout.addWidget(minimumDateEdit, 0, 1);
        dateBoxLayout.addWidget(maximumDateLabel, 2, 0);
        dateBoxLayout.addWidget(maximumDateEdit, 2, 1);
        dateBoxLayout.setRowStretch(3, 1);

        datesGroupBox.setLayout(dateBoxLayout);
    }

    private void createTextFormatsGroupBox() {
        textFormatsGroupBox = new QGroupBox(tr("Text Formats"));

        weekdayColorCombo = createColorComboBox();
        weekdayColorCombo.setCurrentIndex(
                weekdayColorCombo.findText(tr("Black")));

        weekdayColorLabel = new QLabel(tr("&Weekday color:"));
        weekdayColorLabel.setBuddy(weekdayColorCombo);

        weekendColorCombo = createColorComboBox();
        weekendColorCombo.setCurrentIndex(
                weekendColorCombo.findText(tr("Red")));

        weekendColorLabel = new QLabel(tr("Week&end color:"));
        weekendColorLabel.setBuddy(weekendColorCombo);

        headerTextFormatCombo = new QComboBox();
        headerTextFormatCombo.addItem(tr("Bold"));
        headerTextFormatCombo.addItem(tr("Italic"));
        headerTextFormatCombo.addItem(tr("Plain"));

        headerTextFormatLabel = new QLabel(tr("&Header text:"));
        headerTextFormatLabel.setBuddy(headerTextFormatCombo);

        firstFridayCheckBox = new QCheckBox(tr("&First Friday in blue"));

        mayFirstCheckBox = new QCheckBox(tr("May &1 in red"));

        weekdayColorCombo.currentIndexChanged.connect(this, "weekdayFormatChanged()");
        weekendColorCombo.currentIndexChanged.connect(this, "weekendFormatChanged()");
        headerTextFormatCombo.currentStringChanged.connect(this, "reformatHeaders()");
        firstFridayCheckBox.toggled.connect(this, "reformatCalendarPage()");
        mayFirstCheckBox.toggled.connect(this, "reformatCalendarPage()");

        QHBoxLayout checkBoxLayout = new QHBoxLayout();
        checkBoxLayout.addWidget(firstFridayCheckBox);
        checkBoxLayout.addStretch();
        checkBoxLayout.addWidget(mayFirstCheckBox);

        QGridLayout outerLayout = new QGridLayout();
        outerLayout.addWidget(weekdayColorLabel, 0, 0);
        outerLayout.addWidget(weekdayColorCombo, 0, 1);
        outerLayout.addWidget(weekendColorLabel, 1, 0);
        outerLayout.addWidget(weekendColorCombo, 1, 1);
        outerLayout.addWidget(headerTextFormatLabel, 2, 0);
        outerLayout.addWidget(headerTextFormatCombo, 2, 1);
        outerLayout.addLayout(checkBoxLayout, 3, 0, 1, 2);
        textFormatsGroupBox.setLayout(outerLayout);

        weekdayFormatChanged();
        weekendFormatChanged();
        reformatHeaders();
        reformatCalendarPage();
    }

    private QComboBox createColorComboBox() {
        QComboBox comboBox = new QComboBox();
        comboBox.addItem(tr("Red"), new QColor(Qt.GlobalColor.red));
        comboBox.addItem(tr("Blue"), new QColor(Qt.GlobalColor.blue));
        comboBox.addItem(tr("Black"), new QColor(Qt.GlobalColor.black));
        comboBox.addItem(tr("Magenta"), new QColor(Qt.GlobalColor.magenta));
        return comboBox;
    }

    public static void Main(string[] args) {
        QApplication.initialize(args);

        CalendarWidget widget = new CalendarWidget();
        widget.show();

        QApplication.exec();
    }
}

```