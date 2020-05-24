# PyQt

## main program

Using like cpp program structure

```text
if __name__ == '__main__':
  app = QApplication(sys.argv)
  import argparse
  parser = argparse.ArgumentParser(description='fault_sim_gui_json')
  parser.add_argument('-config', metavar='configfile', type=str, required=False,
                      help='fault simulation gui configuration JSON file')

  args = parser.parse_args()
  print(args.config)
  mainWindow = MainWindow(jsonFile = args.config)

  mainWindow.show()
  sys.exit(app.exec_())
```

## Use Ui files

* direct include mainwinodw.ui

  ```text
  from PyQt5 import uic
  base, form = uic.loadUiType('mainwindow.ui')
  class MainWindow(base, form):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
    self.setupUi(self)
  ```

* using pyuic5 and include Ui\_mainwindow.py
  * in Makefile

    ```text
    pyuic5 mainwindow.ui -o Ui_mainwindow.py
    ```

  * in py file

    ```text
    from Ui_mainwindow import Ui_MainWindow
    class MainWindow(QMainWindow, Ui_MainWindow):
    def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
    self.setupUi(self)
    ```

## Use RC files

* using pyrcc5 and include test\_rc.py
  * in Makefile

    ```text
    pyrcc5 test.qrc -o test_rc.py
    ```

  * in py file

    ```text
    import test_rc
    class MainWindow(QMainWindow, Ui_MainWindow):
    def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
    self.setupUi(self)
    file = QFile(':/default.txt')
    ```

## Signals and Slots

* Signals

```text
from PyQt5.QtCore import QObject, pyqtSignal

class Foo(QObject):

    # This defines a signal called 'closed' that takes no arguments.
    closed = pyqtSignal()

    # This defines a signal called 'rangeChanged' that takes two
    # integer arguments.
    range_changed = pyqtSignal(int, int, name='rangeChanged')
```

* Slots

```text
from PyQt5.QtCore import QObject, pyqtSlot

class Foo(QObject):

    @pyqtSlot()
    def foo(self):
        """ C++: void foo() """

    @pyqtSlot(int, str)
    def foo(self, arg1, arg2):
        """ C++: void foo(int, QString) """
```

* connect with signal and slot

```text
class Foo(QObject):
    self.range_changed.connect(self.foo)
```

* emit signal

```text
class Foo(QObject):

    # Define a new signal called 'trigger' that has no arguments.
    trigger = pyqtSignal()

    def connect_and_emit_trigger(self):
        # Connect the trigger signal to a slot.
        self.trigger.connect(self.handle_trigger)

        # Emit the signal.
        self.range_changed.emit(int1, int2)
```

## qtreemodel

The model class need to modify the following functions

* flags\(\) : indicate the cell property, **override**
* data\(\) : get the reall data, **override**
* headerData\(\) : header data, **override**
* columnCount\(\) : column number, **override**
* rowCount\(\) : the QModelIndex row count, **override**
* index\(\) : Build QModelIndex system, **override**
* parent\(\) : Build QModelIndex system, **override**

![qmodelindex](qmodelindex.PNG)

If the model need to modify data, we also need to write the following functions

* setData\(\) : write data, **override**
* setHeaderData : write header data, **override**
* insertRows\(\), **override**
* insertColumns\(\), **override**
* removeRows\(\), **override**
* removeColumns\(\), **override**

## qtreewidget

```text
 ui->treeWidget->setColumnCount(2);
 ui->treeWidget->setHeaderLabels(QStringList() << "ABC" << "123");
 addTreeRoot("A", "Root_first");
 addTreeRoot("B", "Root_second");
 addTreeRoot("C", "Root_third");
 addTreeRoot(name, description) {
    QTreeWidgetItem *treeItem = new QTreeWidgetItem(ui->treeWidget);
    treeItem->setText(0, name);
    treeItem->setText(1, description);
    addTreeChild(treeItem, name + "A", "Child_first");
    addTreeChild(treeItem, name + "B", "Child_second");
 }
 addTreeChild(parent, name, description) {
    QTreeWidgetItem *treeItem = new QTreeWidgetItem();

    treeItem->setText(0, name);
    treeItem->setText(1, description);
    parent->addChild(treeItem);
  }
```

