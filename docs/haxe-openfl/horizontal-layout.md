---
title: How to use HorizontalLayout with Feathers UI containers
sidebar_label: HorizontalLayout
---

The [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html) class may be used to position the children of a container from left to right, in a single row. It supports a number of useful options for adjusting the spacing around the container's children and their alignment within the bounds of the container.

> This layout is designed to be used with basic containers like [`LayoutGroup`](./layout-group.md) and [`ScrollContainer`](./scroll-container.md), which are intended purely for visual layout and do not offer built-in capabilities for rendering data from a collection. If using [`ListView`](./list-view.md), or another container that renders data, consider using other layouts that are optimized for data containers by offering optimizations like layout virtualization.

## The Basics

Create a [`LayoutGroup`](./layout-group.md) container, add it to [the display list](https://books.openfl.org/openfl-developers-guide/display-programming/basics-of-display-programming.html), and then add a few children to the container.

```hx
var container = new LayoutGroup();
this.addChild(container);

var child1 = new Button();
child1.text = "One";
container.addChild(child1);

var child2 = new Button();
child2.text = "Two";
container.addChild(child2);

var child3 = new Button();
child3.text = "Three";
container.addChild(child3);
```

Set the container's [`layout`](https://api.feathersui.com/current/feathers/layout/feathers/controls/LayoutGroup.html#layout) property to a new [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html) instance.

```hx
container.layout = new HorizontalLayout();
```

By default, the first child will be positioned in the top-left corner. Each additional child will be positioned to the right of the previous child — creating a single, horizontal row.

The following sections will introduce a number of properties that may be used to adjust the positioning and sizing of children in the layout.

### Spacing

The _padding_ is the space around the edges of the container that will contain no children. Padding may be added on each side, including [top](https://api.feathersui.com/current/feathers/controls/HorizontalLayout.html#paddingTop), [right](https://api.feathersui.com/current/feathers/controls/HorizontalLayout.html#paddingRight), [bottom](https://api.feathersui.com/current/feathers/controls/HorizontalLayout.html#paddingBottom), and [left](https://api.feathersui.com/current/feathers/controls/HorizontalLayout.html#paddingLeft).

```hx
layout.paddingTop = 10.0;
layout.paddingRight = 15.0;
layout.paddingBottom = 10.0;
layout.paddingLeft = 15.0;
```

The [`gap`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html#gap) refers to the space, measured in pixels, between each child in the container.

```hx
layout.gap = 5.0;
```

### Alignment

The children of the container may be _aligned_ within the container's bounds.

To align the children along the x-axis, set the [`horizontalAlign`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html#horizontalAlign) property.

```hx
layout.horizontalAlign = CENTER;
```

In the example above, the children are aligned to the [center](https://api.feathersui.com/current/feathers/layout/HorizontalAlign.html#CENTER) of the x-axis. They may also be aligned to the [left](https://api.feathersui.com/current/feathers/layout/HorizontalAlign.html#LEFT) or to the [right](https://api.feathersui.com/current/feathers/layout/HorizontalAlign.html#RIGHT).

> **Note:** Horizontal alignment may be used only when the total width of the content (including padding and gap values) is less than or equal to the width of the container. If the content is larger than its parent container, the layout will position the children starting from `0.0` on the x-axis, the same as if they were left-aligned.

To align the children along the y-axis, set the [`verticalAlign`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html#verticalAlign) property.

```hx
layout.verticalAlign = MIDDLE;
```

In the example above, the children are aligned to the [middle](https://api.feathersui.com/current/feathers/layout/VerticalAlign.html#MIDDLE) of the y-axis. They may also be aligned to the [top](https://api.feathersui.com/current/feathers/layout/VerticalAlign.html#TOP) or to the [bottom](https://api.feathersui.com/current/feathers/layout/VerticalAlign.html#BOTTOM).

The [`verticalAlign`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html#verticalAlign) property also supports a special value, named [`JUSTIFY`](https://api.feathersui.com/current/feathers/layout/VerticalAlign.html#JUSTIFY). When vertical justification is specified, the height of all items in the layout is adjusted to fill the full height of the container.

```hx
layout.verticalAlign = JUSTIFY;
```

## Percentage Sizing

The dimensions of each child in a container using [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html) may optionally be set to some percentage of the container's total width or height.

> To see a live demonstration percentage sizing with [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html), open the [_horizontal-layout-percentage-sizing_ sample](https://feathersui.com/samples/haxe-openfl/horizontal-layout-percentage-sizing/) in your web browser.

### percentWidth

When the parent container is using [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html), set the [`percentWidth`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html#percentWidth) property on a [`HorizontalLayoutData`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html) instance, and pass it to a child's [`layoutData`](https://api.feathersui.com/current/feathers/layout/ILayoutObject.html#layoutData) property.

In the following example, two children are added to a container. The first child fills 25% of the container's total width, and the second child fills 75% of the container's total width.

```hx
var container = new LayoutGroup();
container.layout = new HorizontalLayout();
this.addChild(container);

var child1 = new Button();
child1.label = "One";
var layoutData1 = new HorizontalLayoutData();
layoutData1.percentWidth = 25.0;
child1.layoutData = layoutData1;
container.addChild(child1);
 
var child2 = new Button();
child2.label = "Two";
var layoutData2 = new HorizontalLayoutData();
layoutData2.percentWidth = 75.0;
child2.layoutData = layoutData2;
container.addChild(child2);
```

Children with percentage sizing may be combined with children using fixed pixel widths. In that case, [`percentWidth`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html#percentWidth) will be based on the remaining space in the parent container after the fixed pixel width is subtracted from the container's width.

In the following example, we have two children again, but this time, the first child is a fixed `150.0` pixels wide, and the second child uses [`percentWidth`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html#percentWidth). In this situation, the percentage will be based on the width of the container _minus 150.0 pixels_.

```hx
var container = new LayoutGroup();
container.layout = new HorizontalLayout();
this.addChild(container);

var child1 = new Button();
child1.label = "One";
child1.width = 150.0; // pixels
container.addChild(child1);
 
var child2 = new Button();
child2.label = "Two";
var layoutData2 = new HorizontalLayoutData();
layoutData2.percentWidth = 100.0;
child2.layoutData = layoutData2;
container.addChild(child2);
```

Because the first child's width is `150.0` pixels, and not a percentage, the second child's width won't be equal to the width of the container, even though [`percentWidth`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html#percentWidth) is equal to `100.0`. Percentages are always calculated _after_ fixed values in pixels have been subtracted from the container's total size.

### percentHeight

When the parent container is using [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html), set the [`percentHeight`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html#percentHeight) property on a [`HorizontalLayoutData`](https://api.feathersui.com/current/feathers/layout/HorizontalLayoutData.html) instance, and pass it to a child's [`layoutData`](https://api.feathersui.com/current/feathers/layout/ILayoutObject.html#layoutData) property.

In the following example, two children are added to a container. The first child fills 100% of the container's total height, and the second child fills 50% of the container's total height.

```hx
var container = new LayoutGroup();
container.layout = new HorizontalLayout();
this.addChild(container);

var child1 = new Button();
child1.label = "One";
child1.width = 150.0;
var layoutData1 = new HorizontalLayoutData();
layoutData1.percentHeight = 100.0;
child1.layoutData = layoutData1;
container.addChild(child1);
 
var child2 = new Button();
child2.label = "Two";
var layoutData2 = new HorizontalLayoutData();
layoutData2.percentHeight = 50.0;
child2.layoutData = layoutData2;
container.addChild(child2);
```

Notice that we can mix and match fixed pixel values with percentage values on the same child. The width of the first child is `150.0` pixels, but the height is 100% of the container's total height.

> **Tip:** If the height of every child in a [`HorizontalLayout`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html) should be 100%, set [`verticalAlign`](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html#verticalAlign) to [`JUSTIFY`](https://api.feathersui.com/current/feathers/layout/VerticalAlign.html#JUSTIFY). You'll write less code, and performance will be better optimized.

## Related Links

- [`feathers.layout.HorizontalLayout` API Documentation](https://api.feathersui.com/current/feathers/layout/HorizontalLayout.html)