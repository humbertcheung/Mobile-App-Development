# UI拓展与实践（UIPageControl）
## 前言
> 在上一节课中，已经学习了UIScrollView的基本使用，本节课我们会在学习滚动视图的配套控件UIPageControl

我们先回顾一下上节课的内容：


```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{
    
    var scrollView = UIScrollView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setViews()
    }
    func setViews() {
        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.indicatorStyle = .white
        scrollView.delegate = self
        view.addSubview(scrollView)
        
        let content1 = UIImageView(image: UIImage(named: "pic1"))
        content1.frame = CGRect(x: 0, y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        content1.contentMode = .scaleToFill
        scrollView.addSubview(content1)
        
        let content2 = UIImageView(image: UIImage(named: "pic2"))
        content2.frame = CGRect(x: SCREEN_WIDTH, y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        content2.contentMode = .scaleToFill
        scrollView.addSubview(content2)
        
        let content3 = UIImageView(image: UIImage(named: "pic3"))
        content3.frame = CGRect(x: SCREEN_WIDTH * 2, y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        content3.contentMode = .scaleToFill
        scrollView.addSubview(content3)
        
        let content4 = UIImageView(image: UIImage(named: "pic4"))
        content4.frame = CGRect(x: SCREEN_WIDTH * 3, y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        content4.contentMode = .scaleToFill
        scrollView.addSubview(content4)
    }

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.setContentOffset(CGPoint(x: 0, y: 0), animated: true)
        }
    }
}
```

优化一下，去掉冗余代码：


```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{
    
    var scrollView = UIScrollView()
    
    var pics = [UIImage]()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        for i in 1...4 {
            pics.append(UIImage(named: "pic\(i)")!)
        }
        setViews()
    }
    func setViews() {
        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.indicatorStyle = .white
        scrollView.delegate = self
        view.addSubview(scrollView)
        
        for i in 0..<pics.count {
            print("index\(i)")
            let content = UIImageView(image: UIImage(named: "pic\(i+1)"))
            content.frame = CGRect(x: SCREEN_WIDTH * CGFloat(i), y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
            content.contentMode = .scaleToFill
            scrollView.addSubview(content)
        }
    }
    
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.contentOffset.x = 0
        }
    }
}
```

那么，有些情况下滚动视图还会与分页控制器（UIPageControl）结合起来使用，我们可以直接通过滑动手势来滚动显示内容，或者通过点击分页控制器来滚动显示内容。

**UIPageControl使用步骤**：

1. 创建UIPageControl，可以使用构造器创建并设置frame
2. 创建好后需要设置相关的属性，其中属性numberOfPages需要设置，否则不显示白点。另外，当我们如果需要pageConrtol响应点击事件需要通过addTarget方法来绑定事件，使用方式与按钮类似。
3. 然后添加到视图当中，添加过程特别需要注意的是，不能添加到scrollView当中，不然会显示异常。

### 常用属性


```swift
//设置显示的页数        
open var numberOfPages: Int // default is 0        
//设置显示的当前页的索引        
open var currentPage: Int // default is 0. value pinned to 0..numberOfPages-1        
//设置单页时隐藏        
open var hidesForSinglePage: Bool // hide the the indicator if there is only one page. default is NO
//设置当前页显示的颜色        
open var currentPageIndicatorTintColor: UIColor?
//设置其他页显示的颜色 
open var pageIndicatorTintColor: UIColor?
```

如下，我们希望添加一个pageControl，希望能够点击pageControl来滚动视图：

```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{

    var scrollView = UIScrollView()
    var pics = [UIImage]()
    var pageControl = UIPageControl()

    override func viewDidLoad() {
        super.viewDidLoad()
        for i in 1...4 {
            pics.append(UIImage(named: "pic\(i)")!)
        }
        setViews()
    }

    func setViews() {

        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.showsVerticalScrollIndicator = false
        scrollView.showsHorizontalScrollIndicator = false
        scrollView.delegate = self
        view.addSubview(scrollView)

        for i in 0..<pics.count {
            print("index\(i)")
            let content = UIImageView(image: UIImage(named: "pic\(i+1)"))
            content.frame = CGRect(x: SCREEN_WIDTH * CGFloat(i), y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
            content.contentMode = .scaleToFill
            scrollView.addSubview(content)
        }

        pageControl.frame = CGRect(x: SCREEN_WIDTH * 0.25, y: SCREEN_HEIGHT * 0.5, width: SCREEN_WIDTH * 0.5, height: 20)
        //设置当前白点位置
        pageControl.currentPage = 0
        //设置白点的数量
        pageControl.numberOfPages = pics.count
        pageControl.currentPageIndicatorTintColor = UIColor.purple
        pageControl.pageIndicatorTintColor = UIColor.green
        pageControl.addTarget(self, action: #selector(pageChange), for: .valueChanged)
        view.addSubview(pageControl)
    }

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.contentOffset.x = 0
        }
    }

    @objc func pageChange(_ sender: UIPageControl) {
        scrollView.contentOffset.x = CGFloat(sender.currentPage) * SCREEN_WIDTH
    }
}
```

同时，如果我们需要在滚动内容时，pageControl中的白点能够根据内容的分页而变化，则需要调用UIScrollView的代理方法scrollViewDidScroll来实现，在这个方法中，根据内容滚动的偏移量来计算currenPage，```pageControl.currentPage = Int((scrollView.contentOffset.x) / SCREEN_WIDTH)```
如下：

```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{

    var scrollView = UIScrollView()
    var pics = [UIImage]()
    var pageControl = UIPageControl()

    override func viewDidLoad() {
        super.viewDidLoad()
        for i in 1...4 {
            pics.append(UIImage(named: "pic\(i)")!)
        }
        setViews()
    }

    func setViews() {

        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.showsVerticalScrollIndicator = false
        scrollView.showsHorizontalScrollIndicator = false
        scrollView.delegate = self
        view.addSubview(scrollView)

        for i in 0..<pics.count {
            print("index\(i)")
            let content = UIImageView(image: UIImage(named: "pic\(i+1)"))
            content.frame = CGRect(x: SCREEN_WIDTH * CGFloat(i), y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
            content.contentMode = .scaleToFill
            scrollView.addSubview(content)
        }

        pageControl.frame = CGRect(x: SCREEN_WIDTH * 0.25, y: SCREEN_HEIGHT * 0.5, width: SCREEN_WIDTH * 0.5, height: 20)
        //设置当前白点位置
        pageControl.currentPage = 0
        //设置白点的数量
        pageControl.numberOfPages = pics.count
        pageControl.currentPageIndicatorTintColor = UIColor.purple
        pageControl.pageIndicatorTintColor = UIColor.green
        pageControl.addTarget(self, action: #selector(pageChange), for: .valueChanged)
        view.addSubview(pageControl)
    }

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.contentOffset.x = 0
        }
        pageControl.currentPage = Int((scrollView.contentOffset.x) / SCREEN_WIDTH)
        print(pageControl.currentPage)
    }

    @objc func pageChange(_ sender: UIPageControl) {
        scrollView.contentOffset.x = CGFloat(sender.currentPage) * SCREEN_WIDTH
    }
}
```

另外，我们还可以设置自动滚动，需要通过Timer类来实现定时器：


```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{

    var scrollView = UIScrollView()
    var pics = [UIImage]()
    var pageControl = UIPageControl()
    var timer = Timer()

    override func viewDidLoad() {
        super.viewDidLoad()
        for i in 1...4 {
            pics.append(UIImage(named: "pic\(i)")!)
        }
        setViews()

        timer = Timer.scheduledTimer(withTimeInterval: 2.0, repeats: true, block: { (t) in
            if self.scrollView.contentOffset.x > SCREEN_WIDTH * 3 {
                self.scrollView.contentOffset.x = 0
            }else{
                self.scrollView.contentOffset.x += SCREEN_WIDTH
            }
        })
    }

    func setViews() {

        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.showsVerticalScrollIndicator = false
        scrollView.showsHorizontalScrollIndicator = false
        scrollView.delegate = self
        view.addSubview(scrollView)

        for i in 0..<pics.count {
            print("index\(i)")
            let content = UIImageView(image: UIImage(named: "pic\(i+1)"))
            content.frame = CGRect(x: SCREEN_WIDTH * CGFloat(i), y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
            content.contentMode = .scaleToFill
            scrollView.addSubview(content)
        }

        pageControl.frame = CGRect(x: SCREEN_WIDTH * 0.25, y: SCREEN_HEIGHT * 0.5, width: SCREEN_WIDTH * 0.5, height: 20)
        //设置当前白点位置
        pageControl.currentPage = 0
        //设置白点的数量
        pageControl.numberOfPages = pics.count
        pageControl.currentPageIndicatorTintColor = UIColor.purple
        pageControl.pageIndicatorTintColor = UIColor.green
        pageControl.addTarget(self, action: #selector(pageChange), for: .valueChanged)
        view.addSubview(pageControl)
    }

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.contentOffset.x = 0
        }
        pageControl.currentPage = Int((scrollView.contentOffset.x) / SCREEN_WIDTH)
        print(pageControl.currentPage)
    }

    @objc func pageChange(_ sender: UIPageControl) {
        scrollView.contentOffset.x = CGFloat(sender.currentPage) * SCREEN_WIDTH
    }
}
```

不过存在一个bug，当我们在进行拖拽时定时器也在同时进行，两种动作重复进行了，如何让定时器在我们拖拽时停止工作呢，可以通过定时器的一个方法来实现`invalidate()`，如下：

```swift
import UIKit

let SCREEN_HEIGHT = UIScreen.main.bounds.size.height
let SCREEN_WIDTH = UIScreen.main.bounds.size.width

class ViewController: UIViewController, UIScrollViewDelegate{
    
    var scrollView = UIScrollView()
    var pics = [UIImage]()
    var pageControl = UIPageControl()
    var timer = Timer()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        for i in 1...4 {
            pics.append(UIImage(named: "pic\(i)")!)
        }
        setViews()
        setTimer()
    }
    
    func setTimer() {
        
        timer = Timer.scheduledTimer(withTimeInterval: 2.0, repeats: true, block: { (t) in
            if self.scrollView.contentOffset.x > SCREEN_WIDTH * 3 {
                self.scrollView.contentOffset.x = 0
            }else{
                self.scrollView.contentOffset.x += SCREEN_WIDTH
            }
        })
    }
    
    func setViews() {
        
        scrollView.frame = CGRect(x: 0, y: 20, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
        scrollView.backgroundColor = UIColor(red: 194/255.0, green: 183/255.0, blue: 223/255.0, alpha: 1.0)
        //设置scrollView滚动区域宽度为屏幕高度的4倍，宽度等于屏幕宽度一半。
        scrollView.contentSize = CGSize(width: SCREEN_WIDTH * 4, height: SCREEN_HEIGHT * 0.5)
        scrollView.isPagingEnabled = true
        scrollView.showsVerticalScrollIndicator = false
        scrollView.showsHorizontalScrollIndicator = false
        scrollView.delegate = self
        view.addSubview(scrollView)
        
        for i in 0..<pics.count {
            print("index\(i)")
            let content = UIImageView(image: UIImage(named: "pic\(i+1)"))
            content.frame = CGRect(x: SCREEN_WIDTH * CGFloat(i), y: 0, width: SCREEN_WIDTH, height: SCREEN_HEIGHT * 0.5)
            content.contentMode = .scaleToFill
            scrollView.addSubview(content)
        }
        
        pageControl.frame = CGRect(x: SCREEN_WIDTH * 0.25, y: SCREEN_HEIGHT * 0.5, width: SCREEN_WIDTH * 0.5, height: 20)
        //设置当前白点位置
        pageControl.currentPage = 0
        //设置白点的数量
        pageControl.numberOfPages = pics.count
        pageControl.currentPageIndicatorTintColor = UIColor.purple
        pageControl.pageIndicatorTintColor = UIColor.green
        pageControl.addTarget(self, action: #selector(pageChange), for: .valueChanged)
        view.addSubview(pageControl)
    }
    
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        //设置，当滚动到最后一张时，让内容回到第一张
        if scrollView.contentOffset.x > SCREEN_WIDTH * 3.2 {
            scrollView.contentOffset.x = 0
        }
        pageControl.currentPage = Int((scrollView.contentOffset.x) / SCREEN_WIDTH)
        print(pageControl.currentPage)
    }
    
    func scrollViewWillBeginDragging(_ scrollView: UIScrollView) {
        timer.invalidate()
    }
    
    func scrollViewDidEndDragging(_ scrollView: UIScrollView, willDecelerate decelerate: Bool) {
        setTimer()
    }
    
    @objc func pageChange(_ sender: UIPageControl) {
        scrollView.contentOffset.x = CGFloat(sender.currentPage) * SCREEN_WIDTH
    }
}

```

