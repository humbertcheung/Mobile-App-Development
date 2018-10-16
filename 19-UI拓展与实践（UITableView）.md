# UI拓展与实践（UITableView）

## 前言

表示图UITableView是我们开发过程中常见的，也是常用的数据展示和数据管理手段，主要用来展示一系列内容，UITableView是继承UIScrollView的，UIScrollView可以在任意方向滑动，不过UITableView只允许在垂直方向上滚动。UITableView中的内容则是由UITableViewCell来显示。


## 使用步骤

1. 创建UITableView。打开storyboard，在控件库中拖拽一个UITableView到ViewController中。同时将tableView的delete和dataSource设置成当前的视图控制器。

2. 添加原型单元格。在tableView中添加一个原型单元格（Prototype Cell）
3. 遵循协议。然后在UITableView所在类的文件中，遵循delete和dataSource。即在ViewController类的父类后面添加上UITableViewDelegate以及UITableViewDataSource。
4. 实现协议方法。其中有两个方法为UITableViewDataSource要求所必须实现的，否则编译器会报错：
`tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int`
`tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell`

如下：

```swift
import UIKit

class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
//遵循UITableViewDelegate和UITableViewDataSource
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 10
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let Identifier = "ReuseCell"
        
        var cell = tableView.dequeueReusableCell(withIdentifier: Identifier)
        
        if cell == nil {
            
            cell = UITableViewCell(style: .subtitle, reuseIdentifier: Identifier)
        }
        
        cell?.textLabel?.text = "主标题"
        cell?.detailTextLabel?.text = "副标题"
        return cell!
    }
}
```

当然，我们也可以通过纯代码的方式来创建和使用UITableView：


```swift
import UIKit

class NewViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let tableView = UITableView(frame: view.bounds, style: .plain)
        view.addSubview(tableView)
        tableView.dataSource = self
        tableView.delegate = self
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 10
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let Identifier = "ReuseCell"
        var cell = tableView.dequeueReusableCell(withIdentifier: Identifier)
        if cell == nil {
            cell = UITableViewCell(style: .subtitle, reuseIdentifier: Identifier)
        }
        
        cell?.textLabel?.text = "主标题~"
        cell?.detailTextLabel?.text = "这里是内容了🐷"
        cell?.imageView?.image = UIImage(named:"G")
        return cell!
    }
}
```

### UITableView
##### 常用属性与方法

* **dataSource: UITableViewDataSource?** ： 用来指定代理
* **delegate: UITableViewDelegate?** ： 用来指定数据源代理
* **style: UITableView.Style { get }** ：用来获取tableView的样式，为只读属性
* **rowHeight: CGFloat** ：用来设置单元格高度
* **sectionHeaderHeight: CGFloat** ：用来设置分组头部高度
* **sectionFooterHeight: CGFloat** ：用来设置分组尾部高度
* **tableHeaderView: UIView?** ：用来设置表格头部视图
* **tableFooterView: UIView?** ：用来设置表格尾部视图
* 

* **init(frame: CGRect, style: UITableView.Style)** ：常用构造器方法
* **reloadData()** ：刷新表格数据
* **dequeueReusableCell(withIdentifier identifier: String) -> UITableViewCell?** ： 根据identifier从单元格重用池中获取单元格
* **register(_ nib: UINib?, forCellReuseIdentifier identifier: String)** ：为重用ID为identifier的xib文件注册

### UITableViewCell

在UITableView中，内容主要是通过UITableViewCell来展示的，两者间的关系如下：

* UITableView由UITableViewCell组成，通过UITabelViewCell来显示数据。
* UITableView的每一行就是一个cell，就是每一个UITableViewCell显示一条项目。
* UITableViewCell对象的数量不受限制，仅由设备内存决定。
* UITableViewCell类定义了单元格在UITableView中的属性和行为。

##### 常用属性和方法

* **open var imageView: UIImageView? { get }** // default is nil.  image view will be created if necessary. 图片视图，可以用来显示图片。

* **open var textLabel: UILabel? { get }** // default is nil.  label will be created if necessary. 标题文本。

* **open var detailTextLabel: UILabel? { get }** // default is nil.  label will be created if necessary (and the current style supports a detail label). 内容文本，用来展示详细标题。

* **open var contentView: UIView { get }** 内容视图。当我们想要在单元格中加入我们自定义的视图，则可以添加到内容是视图中。

* **open var reuseIdentifier: String? { get }** 可重用标识符，可通过该标识符去重用池中获取重用的单元格。

* **open var isSelected: Bool** // set selected state (title, image, background). default is NO. animated is NO. 用来设置单元格的选中状态。 

* **open var isHighlighted: Bool** // set highlighted state (title, image, background). default is NO. animated is NO. 用来设置单元格的高亮状态

* **open var editingStyle: UITableViewCell.EditingStyle { get }** // default is UITableViewCellEditingStyleNone. 用来设置单元格的编辑样式，默认为none，还有另外两种样式：insert、delete

    
* **open var accessoryType: UITableViewCell.AccessoryType** // default is UITableViewCellAccessoryNone. use to set standard type. 用来设置单元格的附件类型：
    * 打钩，选中（checkmark）
    ![](media/15391544366443/15391810781951.jpg)

    * 圆圈 + 感叹号（detailButton）
   ![](media/15391544366443/15391811888950.jpg)
    * 圆圈 + 感叹号 + 箭头（detailDisclosureButton）
    ![](media/15391544366443/15391812319963.jpg)

    * 箭头（disclosureIndicator）
    ![](media/15391544366443/15391813065933.jpg)


* **open var accessoryView: UIView?** // if set, use custom view. ignore accessoryType. tracks if enabled can calls accessory action. 用来自定义附件类型视图，如果设置了此属性，则会忽略accessoryType属性。


### UITableViewDataSource

在这个协议中提供了一些列数据源方法用来给UITableView提供数据来源的，其中有两个是必须要实现的方法。

* **tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath)**:
主要用来初始化和复用指定索引位置的UITableViewCell，**必须实现**。

* **tableView(_ tableView: UITableView, numberOfRowsInSection section: Int)**:
主要用来设置一组（section）中单元格的数量，**必须实现**。

* **numberOfSections(in tableView: UITableView**):
主要用来设置表格中的分组（section）个数。

* **tableView(_ tableView: UITableView, titleForHeaderInSection section: Int)**:
主要用来设置分组头部的标题文字，如果不设置或代理返回值为nil，则就不显示。

* **tableView(_ tableView: UITableView, titleForFooterInSection section: Int)**:
主要用来设置分组脚部的标题文字，如果不设置或代理返回值为nil，则就不显示。

* **tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath)**:
主要用来设置表格中指定索引位置的cell是否可编辑，可编辑的cell会显示插入或删除的图标。

* **tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath)**:
当完成插入或删除操作时（即用户点击了单元格的操作图标，如点击了删除图标），则会调用此方法。

* **tableView(_ tableView: UITableView, canMoveRowAt indexPath: IndexPath)**:
用来设置指定索引位置的cell是否可以通过拖动的方式，改变它的位置。

* **tableView(_ tableView: UITableView, moveRowAt sourceIndexPath: IndexPath, to destinationIndexPath: IndexPath)**:
当cell从一个位置拖动到另一个位置时调用此方法。

> **注意：**
> 在代理方法中，我们会常见到一个变量：indexPath（NSIndexPath），主要用来描述在嵌套数列的指定节点的路径，所以它是一个索引路径，而不是一个具体的索引值。索引路径中的每一个索引，都用来表示一个节点的子数组中的指定索引位置。事实上，NSIndexPath描述了一整个数列，表示在表格视图中指定的分组中的指定行。
> UITableView中的索引路径包括两个元素，第一个元素section是表格的分组的序号，第二个元素row表示分组中的行序号。


### UITableViewDelegate

UITableViewDelegate代理协议，主要提供了一些与UITableView操作相关的方法，都是可选的，比如控制表格的选择、指定分组的头和尾的显示、单元格内容的复制和粘贴以及协助完成单元格的排序等功能。

* **tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)**:
当用户点击选择指定索引位置的单元格时，调用此方法。

* **tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath)**:
主要用来设置单元格高度，每当单元格需要显示时，都会调用此方法。

* **tableView(_ tableView: UITableView, heightForHeaderInSection section: Int)**：
主要用来设置某一索引下的分组头部的高度。

* **tableView(_ tableView: UITableView, heightForFooterInSection section: Int)**:
主要用来设置某一索引下的分组尾部的高度。

* **tableView(_ tableView: UITableView, willDisplay cell: UITableViewCell, forRowAt indexPath: IndexPath)**:
当指定索引位置上的单元格即将显示时，调用此方法。此方法是委托对象有机会在单元格显示之前重写其状态属性，如背景颜色等。

* **tableView(_ tableView: UITableView, didDeselectRowAt indexPath: IndexPath)**:
当用户点击一个已经被选中的单元格时，调用此方法。


