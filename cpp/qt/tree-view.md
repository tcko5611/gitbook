# Tree View



## Basic structure \(TreeItem\)

* The tree item need to have the following data
  * how to get it child : **getChild\(num\)**
  * how many row they have : **childCount\(\)**
  * how many column they have : **columnCount\(\)**
  * who is it parent: **parent\(\)**
  * it index of the parent: **childNumber\(\)** 
  * get the value in column : **data\(\)**

## The QAbstracyItemModel for QTreeView

* The ItemModel need to rewrite the folloing pure virtual functions at least
  * data : return a QModelIndex data
  * index : create a QModelIndex from top
  * parent : create a QModelIndex from bottom
  * rowCount : return the row for the QModelIndex
  * columnCount : return the column for the QModelIndex 

## link QAbstrctItemModel and QTreeView

* view-&gt;setModel\(model\) can show the results of model.

