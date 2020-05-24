# Qt



## Signal Slot

### Basic mechanism

A simple code for signal and slot is like follows:

```text
#include <QObject>
class Counter : public QObject
{
    Q_OBJECT
public:
    Counter() { m_value = 0; }
    int value() const { return m_value; }

public slots:
    void setValue(int value);

signals:
    void valueChanged(int newValue);

private:
    int m_value;
};
```

When use signal, it will need _moc_ to generate signal information. The slot functions are general member functions.

```text
connect(sender, SIGNAL(destroyed(QObject*)), this, SLOT(objectDestroyed(Qbject*)));
```

### use make and cmake to generate moc, qrc, ui files

when use make to generate moc, qrc, ui files, we need to write the following code in Makefile

```text
QT_MOCSOURCE = $(addprefix moc_, $(addsuffix .cpp, $(basename $(QT_MOCFILE))))
QT_RCCSOURCE = $(addprefix qrc_, $(addsuffix .cpp, $(basename $(QT_RCCFILE))))
QT_UICSOURCE = $(addprefix ui_, $(addsuffix .h, $(basename $(QT_UICFILE))))
```

when use cmake, we just need to add the following line in CMakeLists.txt

```text
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
```

### Where is the thread that slot functions evaluate?

In the end of connect, we can add connection type like:

```text
connect(socket, SIGNAL(readyRead()), this, SLOT(readyRead()), Qt::DirectConnection);
```

The last argument is about how the slot is executed in which thread, there 4 types:

* Qt::AutoConnection 0 \(default\) If the signal is emitted from a different 

  thread than the receiving object, the signal is queued, 

  behaving as Qt::QueuedConnection. 

  Otherwise, the slot is invoked directly, behaving as Qt::DirectConnection. 

  The type of connection is determined when the signal is emitted.

* Qt::DirectConnection  1  The slot is invoked immediately, 

  when the signal is emitted.

* Qt::QueuedConnection  2  The slot is invoked when control returns to the 

  event loop of the receiver's thread. 

  The slot is executed in the receiver's thread.

* Qt::BlockingQueuedConnection 4 Same as QueuedConnection, except the 

  current thread blocks until the slot returns. 

  This connection type should only be used where the emitter and 

  receiver are in different threads.

It's especially import for GUI and Nerwork problems.

## GUI

### QMainWindow

Create a main window like follows:

```text
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    ....
}

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    // QApplication a(argc, argv); /* for GUI application */
    ...
    return a.exec();
}
```

### QTableWidget

Create a table widget like follows:

```text
ui->tableWidget->setColumnCount(5); 
ui->tableWidget->setShowGrid(true); 
ui->tableWidget->setSelectionMode(QAbstractItemView::SingleSelection);
ui->tableWidget->setSelectionBehavior(QAbstractItemView::SelectRows);
ui->tableWidget->setHorizontalHeaderLabels(headers); // QStringLIst
ui->tableWidget->horizontalHeader()->setStretchLastSection(true);
ui->tableWidget->hideColumn(0);
ui->tableWidget->insertRow(i);
ui->tableWidget->setItem(i,0, new QTableWidgetItem(query.value(0).toString()));
QTableWidgetItem *item = new QTableWidgetItem();
item->data(Qt::CheckStateRole);
/* Check on the status of odd if an odd device, 
 * exhibiting state of the checkbox in the Checked, Unchecked otherwise
 * */
if(query.value(1).toInt() == 1){
  item->setCheckState(Qt::Checked);
} else {
  item->setCheckState(Qt::Unchecked);
}
// Set the checkbox in the second column
ui->tableWidget->setItem(i,1, item);
/ Next, pick up all the data from a result set in other fields
ui->tableWidget->setItem(i,2, new QTableWidgetItem(query.value(2).toString()));
ui->tableWidget->setItem(i,3, new QTableWidgetItem(query.value(3).toString()));
ui->tableWidget->setItem(i,4, new QTableWidgetItem(query.value(4).toString()));
```

## Tcp

### QTcpServer

The sample code for thread is like:

```text
#ifndef MYTHREAD_H
#define MYTHREAD_H

#include <QThread>
#include <QTcpSocket>
#include <QDebug>

class MyThread : public QThread
{
    Q_OBJECT
public:

    explicit MyThread(qintptr ID, QObject *parent = 0);
// open socket
// connect ocket readRead, socket  disconnected signals, 
// enter event loop with exec()
    void run();

signals:
    void error(QTcpSocket::SocketError socketerror);

public slots:
// read data and write data
    void readyRead();
    // when disconnected , deleteLater self
    void disconnected();

private:
    QTcpSocket *socket;
    qintptr socketDescriptor;
};

#endif // MYTHREAD_H
```

The sample code for QTcpServer is like:

```text
// myserver.h

#ifndef MYSERVER_H
#define MYSERVER_H

#include <QTcpServer>

class MyServer : public QTcpServer
{
    Q_OBJECT
public:
    explicit MyServer(QObject *parent = 0);
    void startServer(); // listen to server, port
signals:

public slots:

protected:
// start listen thread and connect thread finished to  deleteLater
    void incomingConnection(qintptr socketDescriptor);

};

#endif // MYSERVER_H
```

### QTcpSocket for client

The sample code for client is like:

```text
#ifndef MYTCPSOCKET_H
#define MYTCPSOCKET_H

#include <QObject>
#include <QTcpSocket>
#include <QDebug>

class MyTcpSocket : public QObject
{
    Q_OBJECT
public:
    explicit MyTcpSocket(QObject *parent = 0);

    void doConnect();

signals:
  void finished(); // for finished, connect to app.quit()
public slots:
  void connected(); // for socket connected, start talking
  void readyRead(); //  for socket data ready to read
private:
    QTcpSocket *socket;

};
```

It's interesting client work as a listener to socket, so it need to enter event loop by QCoreApplication.

## Error

### QXcbConnection: XCB error

* Error : QXcbConnection: XCB error: 3...
* Solution: export QT\_LOGGING\_RULES='_.debug=false;qt.qpa._=false'

