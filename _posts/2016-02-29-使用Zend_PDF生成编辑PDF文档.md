---
layout: post
title:  "使用Zend_PDF自动生成、编辑PDF文档"
date:   2016-02-29
excerpt: "PDF格式是目前广泛使用的文档格式。如果你的网站需要提供用户可打印的票据、文档等，你可能会尝试编写F格式的代码。通过这篇文章你可以看到使用Zend_PDF来自动生成PDF票据的过程。"
tag:
- Zend
- PHP
- Code
- PDF
comments: true
---

PDF格式是目前广泛使用的文档格式。如果你的网站需要提供用户可打印的票据、文档等，你可能会尝试编写F格式的代码。通过这篇文章你可以看到使用Zend_PDF来自动生成PDF票据的过程。

### Getting Started ###

Zend_PDF 同时允许生成新PDF文件或导入已存在的PDF文件。更好的选择是，我们先手动创建一个PDF模版，包括了必要的信息，然后我们可以通过Zend_PDF和我们自己编写的代码来导入这个模板并更新它。

下图所示为我们演示使用的PDF模版。
<img src="http://ohmyselina.net/wp-content/uploads/2016/02/template.png" alt="template" width="465" height="581" class="aligncenter size-full wp-image-422" />

如果这是你第一次使用Zend框架，到Zend官方网站 www.zendframework.com 下载最新版并解压到你的服务器。然后在你的代码头写上以下代码来配置 autoloader。

{% highlight php %}
<?php
define("ZF_PATH", realpath("/path/to/zf/library/"));
set_include_path(get_include_path() . PATH_SEPARATOR . ZF_PATH);
require_once "Zend/Loader/Autoloader.php";
$loader = Zend_Loader_Autoloader::getInstance();
{% endhighlight %}

记得把ZF_PATH定义成Zend框架的library路径。

> 注：我们使用的Zend框架为ZF1，而ZF2并不与ZF1兼容。因此本文使用的ZF版本为	Zend Framework 1.12.17

### 导入PDF模版 ###

使用静态方法 load() 导入PDF模版，使用 page 方法访问模板的第一页 。

{% highlight php %}
<?php
// load the invoice
$invoice = Zend_Pdf::load("/path/to/invoice-template.pdf");

// access the first page
$page = $invoice->pages[0];
{% endhighlight %}

### 指定字体和颜色 ###

当你要在PDF文件上写字时，实际上你是在上面画一些像字的形状和线条。这些形状和线条根据你所指定的字体和颜色来组成我们输入的文本。

我们通过使用静态方法 fontWithPath() 或 fontWithName() 来指定我们需要的字体。

{% highlight php %}
<?php
//loading a font by its name
$font = Zend_Pdf_Font::fontWithName(Zend_Pdf_Font::FONT_TIMES_BOLD);

//load font from file system
$font = Zend_Pdf_Font::fontWithPath("/path/to/myfont.ttf");
{% endhighlight %}

然后使用 setFont() 实例方法来指定字体和字号。

{% highlight php %}
<?php
$page->setFont($font, 12);
{% endhighlight %}

指定字体颜色并不是必要进行的步骤。如果不去指定它，黑色会成为默认字体颜色。Zend_PDF 支持三种不同的颜色空间，你可以使用灰度、RGB或者CMYK来指定想要得到的颜色。或者，你也可以使用HTML-style的颜色标记法。

{% highlight php %}
<?php
// Gray Scale colors range from 0.0 (black) to 1.0 (white)
$color = new Zend_Pdf_Color_GrayScale(0.7);

// RGB uses 3 float values from 0.0 to 1.0 for each color component
$color = new Zend_Pdf_Color_Rgb($r,$g,$b);

// CMYK uses 4 float values from 0.0 to 1.0 for each color component
$color = new Zend_Pdf_Color_Cmyk($c,$m,$y,$k);

// HTML uses any valid color name or hex notation
$color = new Zend_Pdf_Color_HTML("blue");
$color = new Zend_Pdf_Color_HTML("#FF52ED");
{% endhighlight %}

然后使用 setFillColor() 方法指定颜色。

{% highlight php %}
<?php
$page->setFillColor($color);
{% endhighlight %}

### 添加内容 ###

在页面上编辑内容使用了 Zend_Pdf_Page 的 drawText() 实例方法。一个X轴，Y轴的坐标集合能够帮助你定位你将要添加内容的位置。坐标轴的定义了页面的左下角为起始点（0, 0）。X轴指向右边，Y轴指向上边。

<img src="http://ohmyselina.net/wp-content/uploads/2016/02/page-x-y.png" alt="page-x-y" width="200" height="235" class="aligncenter size-full wp-image-432" />

在实际应用中你需要按照比例尺计算页面的X轴Y轴坐标，但在这个例子中我将使用以下数据。

{% highlight php %}
<?php
$customerName = "Angelina Jolie";
$invoiceId = "DF-00025786423";

// items in the array are product description,
// quantity purchased, unit price, and total price
$items = array(array("Golden Globe Polish", 1, 25.50, 25.50),
               array("Trophy Shelf", 2, 180.00, 360.00),
               array("DIY Tattoo Kit", 1, 149.99, 149.99));

$subtotal = 535.49;
$discount = 10;
$amountDue = 481.94;

// specify font
$fontBold = Zend_Pdf_Font::fontWithName(Zend_Pdf_Font::FONT_TIMES_BOLD);
$page->setFont($fontBold, 12);

// specify color
$color = new Zend_Pdf_Color_HTML("navy");
$page->setFillColor($color);

$page->drawText($customerName, 110,641);
// another font
$fontNormal = Zend_Pdf_Font::fontWithName(Zend_Pdf_Font::FONT_TIMES);
$page->setFont($fontNormal, 12);

// invoice information
$page->drawText($invoiceId, 420,642);
$page->drawText(date("M d, Y"), 420,628);
$page->drawText('$' . number_format($subtotal, 2), 510,143);
$page->drawText($discount . "%", 510,123);
$page->drawText('$' . number_format($amountDue, 2), 510,103);

// purchase items
$posY = 560;
foreach ($itmes as $item) {
    $page->drawText($item[0], 50, $posY);
    $page->drawText($item[1], 350, $posY);
    $page->drawText(number_format($item[2], 2), 430, $posY);
    $page->drawText(number_format($item[3], 2), 510, $posY);
    $posY -= 22.7;
}
{% endhighlight %}

### 导出票据 ###

在向模板添加完内容后，你可以轻松的保存它（注意不要覆盖了你的模版），或者是将它发送到浏览器。使用 Zend_Pdf 的实例方法 save() 来保存。

{% highlight php %}
<?php
$invoice->save($pathToFile);
{% endhighlight %}

使用实例方法 render() 将会把PDF文件返回成一个字符串。你可以通过 HTTP headers 把得到的PDF文件输出。

{% highlight php %}
<?php
// instruct browser to download the PDF
header("Content-Type: application/x-pdf");
header("Content-Disposition: attachment; filename=invoice-". date("Y-m-d-H-i") . ".pdf");
header("Cache-Control: no-cache, must-revalidate");

// output the PDF
echo $invoice->render();
{% endhighlight %}
