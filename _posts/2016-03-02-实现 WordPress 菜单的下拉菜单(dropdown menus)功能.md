---
layout: post
title:  "实现 WordPress 菜单的下拉菜单(dropdown menus)功能"
date:   2016-03-02
excerpt: "这个教程将教会你使用 PHP 和 CSS 来实现 WordPress 中的下拉菜单(dropdown menus)。"
tag:
- WordPress
- dropdown
- PHP
- CSS
- code
- Walter
comments: true
---

这个教程将教会你使用 PHP 和 CSS 来实现 WordPress 中的下拉菜单(dropdown menus)。

### Demo ###

<a href="http://198.199.116.214/wp-content/uploads/2016/03/demo-1.png" rel="attachment wp-att-465"><img src="http://198.199.116.214/wp-content/uploads/2016/03/demo-1.png" alt="demo" width="755" height="400" class="aligncenter size-full wp-image-465" /></a>

### Step 1: 下载 CSS 源码 ###

下载地址： <a href="http://198.199.116.214/wp-content/uploads/2016/03/source.zip"rel="">Download Source</a>

### Step 2: 上传文件 ###

解压 cssmenu 并上传整个文件夹到 WordPress 的模板(template)目录。

### Step 3: 在 WordPress 中建立菜单 ###

在 外观 > 菜单 中新建并保存自定义的菜单。注意你的菜单名称，下一步中我们会用到它（我的是Main Navigation）。

<a href="http://198.199.116.214/wp-content/uploads/2016/03/wordpress_3_drop_down_menu_tutorial.png" rel="attachment wp-att-451"><img src="http://198.199.116.214/wp-content/uploads/2016/03/wordpress_3_drop_down_menu_tutorial.png" alt="wordpress_3_drop_down_menu_tutorial" width="907" height="463" class="aligncenter size-full wp-image-451" /></a>

### Step 4: 导入 CSS Styles ###

在 functions.php 文件中添加几行代码，将我们刚刚下载的 CSS 文件包含进 WordPress 中。几乎所有的 WordPress 模板都有 functions.php 这个文件，如果你在你的模板目录下没有发现它，直接创建一个也是可以的。

{% highlight php %}
add_action('wp_enqueue_scripts', 'cssmenumaker_scripts_styles' );
function cssmenumaker_scripts_styles() {
   wp_enqueue_style( 'cssmenu-styles', get_template_directory_uri() . '/cssmenu/styles.css');
}
{% endhighlight %}

### Step 5: 添加自定义 Class 到 functions.php ###

添加这个 Class 会使我们的 CSS 代码在模板中运行。

{% highlight php %}
class CSS_Menu_Maker_Walker extends Walker {

  var $db_fields = array( 'parent' => 'menu_item_parent', 'id' => 'db_id' );

  function start_lvl( &$output, $depth = 0, $args = array() ) {
    $indent = str_repeat("\t", $depth);
    $output .= "\n$indent<ul>\n";
  }

  function end_lvl( &$output, $depth = 0, $args = array() ) {
    $indent = str_repeat("\t", $depth);
    $output .= "$indent</ul>\n";
  }

  function start_el( &$output, $item, $depth = 0, $args = array(), $id = 0 ) {

    global $wp_query;
    $indent = ( $depth ) ? str_repeat( "\t", $depth ) : '';
    $class_names = $value = '';
    $classes = empty( $item->classes ) ? array() : (array) $item->classes;

    // Add active class
    if(in_array('current-menu-item', $classes)) {
      $classes[] = 'active';
      unset($classes['current-menu-item']);
    }

    // Check for children
    $children = get_posts(array('post_type' => 'nav_menu_item', 'nopaging' => true, 'numberposts' => 1, 'meta_key' => 'menu_item_menu_item_parent', 'meta_value' => $item->ID));
    if (!empty($children)) {
      $classes[] = 'has-sub';
    }

    $class_names = join( ' ', apply_filters( 'nav_menu_css_class', array_filter( $classes ), $item, $args ) );
    $class_names = $class_names ? ' class="' . esc_attr( $class_names ) . '"' : '';

    $id = apply_filters( 'nav_menu_item_id', 'menu-item-'. $item->ID, $item, $args );
    $id = $id ? ' id="' . esc_attr( $id ) . '"' : '';

    $output .= $indent . '<li' . $id . $value . $class_names .'>';

    $attributes  = ! empty( $item->attr_title ) ? ' title="'  . esc_attr( $item->attr_title ) .'"' : '';
    $attributes .= ! empty( $item->target )     ? ' target="' . esc_attr( $item->target     ) .'"' : '';
    $attributes .= ! empty( $item->xfn )        ? ' rel="'    . esc_attr( $item->xfn        ) .'"' : '';
    $attributes .= ! empty( $item->url )        ? ' href="'   . esc_attr( $item->url        ) .'"' : '';

    $item_output = $args->before;
    $item_output .= '<a'. $attributes .'><span>';
    $item_output .= $args->link_before . apply_filters( 'the_title', $item->title, $item->ID ) . $args->link_after;
    $item_output .= '</span></a>';
    $item_output .= $args->after;

    $output .= apply_filters( 'walker_nav_menu_start_el', $item_output, $item, $depth, $args );
  }

  function end_el( &$output, $item, $depth = 0, $args = array() ) {
    $output .= "</li>\n";
  }
}
{% endhighlight %}

### Step 6: 在模板文件中输出菜单 ###

现在我们需要选择相应的地方显示我们的下拉列表菜单了（dropdown menus）。

我选择模板中的 header.php 中的相应位置添加以下代码。

{% highlight php %}
<?php
wp_nav_menu(array(
  'menu' => 'Main Navigation',
  'container_id' => 'cssmenu',
  'walker' => new CSS_Menu_Maker_Walker()
));
?>
{% endhighlight %}

1. 'menu' => 是我们在 Step 3 中创建的菜单名字
2. 'container_id' => 这个 id 是我们在 CSS 代码中使用的 id
3. 'walker' => 说明我们将使用自定义的 Walker class 来输出 HTML

### Step 7: 检查代码是否生效 ###

如果没有问题的话你就会看到下拉菜单在你的 WordPress 中的表现样式了。

有问题的话可以留言给我。
