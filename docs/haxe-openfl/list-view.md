---
title: How to use the ListView component
sidebar_label: ListView
---

The [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) class renders the items from a [data collection](./data-collections.md). List views support selecting an item, scrolling, and custom layouts.

## The Basics

Start by creating a [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) control, pass in a [collection](./data-collections.md) that defines the items to render, and add it to [the display list](https://books.openfl.org/openfl-developers-guide/display-programming/basics-of-display-programming.html).

```hx
var listView = new ListView();
listView.dataProvider = new ArrayCollection([
    { text: "A" },
    { text: "B" },
    { text: "C" }
]);
this.addChild(listView);
```

Set the [`itemToText()`](https://api.feathersui.com/current/feathers/controls/ListView.html#itemToText) method to get the text from each item to display in an item renderer.

```hx
listView.itemToText = function(item:Dynamic):String {
    return item.text;
};
```

> Items in the collection are not required to be simple object literals, like `{ text: "A" }` in the example above. Instances of a class are allowed too (and encouraged as a best practice).

### Selection

[Add an event listener](https://books.openfl.org/openfl-developers-guide/handling-events/basics-of-handling-events.html) for [`Event.CHANGE`](https://api.openfl.org/openfl/events/Event.html#CHANGE) to perform an action when the user selects a different item.

```hx
listView.addEventListener(Event.CHANGE, listView_changeHandler);
```

Check for the new value of the [`selectedItem`](https://api.feathersui.com/current/feathers/controls/ListView.html#selectedItem) property in the listener.

```hx
function listView_changeHandler(event:Event):Void {
    var listView = cast(event.currentTarget, ListView);
    trace("ListView selectedItem change: " + listView.selectedItem.text);
}
```

Alternatively, the value of the [`selectedIndex`](https://api.feathersui.com/current/feathers/controls/ListView.html#selectedIndex) property references the index of the items in the list view's collection, in the order that they were added.

```hx
function listView_changeHandler(event:Event):Void {
    var listView = cast(event.currentTarget, ListView);
    trace("ListView selectedIndex change: " + listView.selectedIndex);
}
```

## Add or remove items

To add a new item at the end, pass an object to the data provider's [`add()`](https://api.feathersui.com/current/feathers/data/IFlatCollection.html#add) method.

```hx
var newItem = { text: "New Item" };
listView.dataProvider.add(newItem);
```

To add a new item at a specific position, pass an object to the data provider's [`addAt()`](https://api.feathersui.com/current/feathers/data/IFlatCollection.html#addAt) method.

```hx
var newItem = { text: "First Item" };
listView.dataProvider.addAt(newItem, 0);
```

In the example above, a new item is added to the beginning.

Similarly, to remove an item, call [`remove()`](https://api.feathersui.com/current/feathers/data/IFlatCollection.html#remove) or [`removeAt()`](https://api.feathersui.com/current/feathers/data/IFlatCollection.html#removeAt) on the collection.

```hx
listView.dataProvider.removeAt(0);
```

## Item renderers

An _item renderer_ is a Feathers UI component that renders a single item from a [data collection](./data-collections.md) inside a component like [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html). In other words, a [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) typically contains multiple item renderers — with each one rendering a different item from the data provider.

Feathers UI provides a default item renderer that simply displays text. However, components like [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) may also use custom item renderers, which allows developers to render the list view's data in infinite unique ways.

The [`itemRendererRecycler`](https://api.feathersui.com/current/feathers/controls/ListView.html#itemRendererRecycler) property may be used to customize the item renderers in a [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html).

Consider a collection of items with the following format.

```hx
{ name: "Pizza", icon: "https://example.com/img/pizza.png" }
```

The examples above demonstrated how to use an [`itemToText()`](https://api.feathersui.com/current/feathers/controls/ListView.html#itemToText) method to interpret the text. In this case, the `name` field is used.

```hx
listView.itemToText = function(item:Dynamic):String {
    return item.name;
};
```

However, the default item renderer cannot display the image, so a custom item renderer is required.

> Note: Future updates to Feathers UI will probably contain a more powerful default item renderer that _can_ display an image. For now, the best option is to create a custom item renderer.

The following example creates a [`DisplayObjectRecycler`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) which instantiates a [`LayoutGroup`](./layout-group.md) container. A custom item renderer may be created from any OpenFL display object. It is not strictly required to be a Feathers UI component.

```hx
var recycler = DisplayObjectRecycler.withFunction(() -> {
    var itemRenderer = new LayoutGroup();

    var layout = new HorizontalLayout();
    layout.gap = 6.0;
    layout.paddingTop = 4.0;
    layout.paddingBottom = 4.0;
    layout.paddingLeft = 6.0;
    layout.paddingRight = 6.0;
    itemRenderer.layout = layout;

    var icon = new AssetLoader();
    icon.name = "loader";
    itemRenderer.addChild(icon);

    var label = new Label();
    label.name = "label";
    itemRenderer.addChild(label);

    return itemRenderer;
});
listView.itemRendererRecycler = recycler;
```

This creates the item renderer, but it doesn't interpret the data yet. A custom [`update()`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) method on the [`DisplayObjectRecycler`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) can do that.

```hx
recycler.update = (itemRenderer:LayoutGroup, state:ListViewItemState) -> {
    var label = cast(itemRenderer.getChildByName("label"), Label);
    var loader = cast(itemRenderer.getChildByName("loader"), AssetLoader);

    label.text = state.text;
    loader.source = state.data.icon;
};
```

When the [`update()`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) method is called, it receives the item renderer and an [`ListViewItemState`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html) object. [`ListViewItemState`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html) has a number of useful properties.

- [`data`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#data) is the item from the collection.
- [`enabled`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#enabled) indicates if the item renderer should be enabled or not.
- [`index`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#index) is the position of the item within the collection.
- [`owner`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#owner) is the [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) that contains the item.
- [`selected`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#selected) is populated by comparing to [`selectedItem`](https://api.feathersui.com/current/feathers/controls/ListView.html#selectedItem).
- [`text`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#text) is populated using [`itemToText()`](https://api.feathersui.com/current/feathers/controls/ListView.html#itemToText)

In this case, the value of [`text`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#text) is displayed by the [`Label`](./label.md), and the `icon` field from [`data`](https://api.feathersui.com/current/feathers/data/ListViewItemState.html#data) (remember the example item from above, with `name` and `icon` fields) is displayed by the [`AssetLoader`](./asset-loader.md).

It's always a good practice to provide a [`reset()`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) method to the [`DisplayObjectRecycler`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html), which will clean up a custom item renderer when it is no longer used by the [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html).

```
recycler.reset = (itemRenderer:LayoutGroup, state:ListViewItemState) -> {
    var label = cast(itemRenderer.getChildByName("label"), Label);
    var loader = cast(itemRenderer.getChildByName("loader"), AssetLoader);

    label.text = "";
    loader.source = null;
};
```

> A [`DisplayObjectRecycler`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) without a [`reset()`](https://api.feathersui.com/current/feathers/utils/DisplayObjectRecycler.html) method could potentially cause memory leaks or other unexpected behavior, if the same data needs to be used again later.

## Styles

A number of styles may be customized on a [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) component, including an optional background skin and the appearance of the list view's scroll bars.

### Background skin

Optionally give the list view a background using the [`backgroundSkin`](https://api.feathersui.com/current/feathers/controls/supportClasses/BaseScrollContainer.html#backgroundSkin) property. The following example sets it to a [`RectangleSkin`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html) instance.

```hx
var skin = new RectangleSkin();
skin.border = SolidColor(1.0, 0x999999);
skin.fill = SolidColor(0xcccccc);
skin.width = 16.0;
skin.height = 16.0;
listView.backgroundSkin = skin;
```

The [`border`](https://api.feathersui.com/current/feathers/skins/BaseGraphicsPathSkin.html#border) and [`fill`](https://api.feathersui.com/current/feathers/skins/BaseGraphicsPathSkin.html#fill) properties of the [`RectangleSkin`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html) are used to adjust its appearance. They support a variety of values — from solid colors to gradients to bitmaps.

The list view automatically calculates its preferred size based on the initial dimensions of its background skin (accounting for some other factors too, like the layout and scroll bars), so it's important to set a skin's `width` and `height` properties to appropriate values to use in this calculation.

> See [Graphics API skins](./graphics-api-skins.md) for more details about how to use [`RectangleSkin`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html) with the [`LineStyle`](https://api.feathersui.com/current/feathers/graphics/LineStyle.html) and [`FillStyle`](https://api.feathersui.com/current/feathers/graphics/FillStyle.html) enums that change its border and fill appearance.

The appearance of the list view's border or fill may be customized to change when the list view is [disabled](https://api.feathersui.com/current/feathers/core/IUIControl.html#enabled). In the next example, setting the skin's [`disabledFill`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html#disabledFill) method makes it switch to a different fill when the list view is disabled.

```hx
skin.disabledFill = SolidColor(0xffcccc);
```

Similarly, use the skin's [`disabledBorder`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html#disabledBorder) property to change the border when disabled

```hx
skin.disabledBorder = SolidColor(2.0, 0x999999);
```

In the examples above, the list view uses the same [`RectangleSkin`](https://api.feathersui.com/current/feathers/skins/RectangleSkin.html) for all states, and that skin listens for changes to the list view's current state. Alternatively, the list view's [`disabledBackgroundSkin`](https://api.feathersui.com/current/feathers/controls/supportClasses/BaseScrollContainer.html#disabledBackgroundSkin) method allows the list view to display a completely different display object when it is disabled.

```hx
var defaultSkin = new RectangleSkin();
// ... set border, fill, width, and height
listView.backgroundSkin = defaultSkin;

var disabledSkin = new RectangleSkin();
// ... set border, fill, width, and height
listView.disabledBackgroundSkin = disabledSkin;
```

In the example above, the list view will have a separate skins when enabled and disabled.

### Layout

Set the list view's [`layout`](https://api.feathersui.com/current/feathers/layout/feathers/controls/ListView.html#layout) property to change how its children are positioned and sized. By default, a list view uses [`VerticalListLayout`](./vertical-list-layout.md), but it may be changed to a different layout, if desired.

```hx
listView.layout = new HorizontalListLayout();
```

The example above uses [`HorizontalListLayout`](./horizontal-list-layout.md), but a number of [different layouts](./layouts-and-containers.md) are available in Feathers UI, and it's also possible to create custom layouts.

### Scroll bars

The scroll bars in a [`ListView`](https://api.feathersui.com/current/feathers/controls/ListView.html) component are of type [`HScrollBar` and `VScrollBar`](./scroll-bar.md). Their appearance may be customized globally in a [theme](./themes.md), or they may be customized outside of a theme on an specific, individual list view.

> See [How to use the `HScrollBar` and `VScrollBar` components](./scroll-bar.md#styles) for complete details about which styles are available for the scroll bars.

#### Style scroll bars globally

Use the [`HScrollBar`](https://api.feathersui.com/current/feathers/controls/HScrollBar.html) and [`VScrollBar`](https://api.feathersui.com/current/feathers/controls/VScrollBar.html) classes in a [theme](./themes.md) to provide a function that globally styles all scroll bars in your project.

```hx
styleProvider.setStyleFunction(HScrollBar, null, setHScrollBarStyles);
styleProvider.setStyleFunction(VScrollBar, null, setVScrollBarStyles);
```

The functions should use the following signatures.

```hx
function setHScrollBarStyles(scrollBar:HScrollBar):Void {
    // ... set styles here
});

function setVScrollBarStyles(scrollBar:VScrollBar):Void {
    // ... set styles here
});
```

#### Style scroll bars in a specific `ListView`

The [`scrollBarXFactory`](https://api.feathersui.com/current/feathers/controls/supportClasses/BaseScrollContainer.html#scrollBarXFactory) and [`scrollBarYFactory`](https://api.feathersui.com/current/feathers/controls/supportClasses/BaseScrollContainer.html#scrollBarYFactory) properties may be used to customize the creation of an individual list view's scroll bars.

```hx
listView.scrollBarXFactory = () -> {
    var scrollBar = new HScrollBar();
    // ... set styles here
    return scrollBar;
};

listView.scrollBarYFactory = () -> {
    var scrollBar = new VScrollBar();
    // ... set styles here
    return scrollBar;
};
```

## Related Links

- [`feathers.controls.ListView` API Documentation](https://api.feathersui.com/current/feathers/controls/ListView.html)
