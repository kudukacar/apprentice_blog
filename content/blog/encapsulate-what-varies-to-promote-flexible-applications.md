---
path: /encapsulate
date: 2021-04-27T12:48:48.951Z
title: Encapsulate what varies to promote flexible applications
description: >-
  A blog describing the first of nine object oriented principles from Head First
  Design Patterns
---
Head First Design Patterns by Eric Freeman and Elisabeth Robson discuss patterns, promoting flexible applications. Flexible applications minimize interdependency between objects, and reduce the impact of change. Applications incorporating these patterns follow a set of object oriented design principles, one of which is “Encapsulate what varies”.

This design principle refers to the practice of encapsulating code that changes often, for example, with every new requirement. This encapsulated code can then be extended with new requirements without affecting other parts of the application. For example, consider the Gilded Rose Inn with an inventory of various items, changing in quality at different rates. Every day, Gilded Rose updates the quality of the various products. A potential design could look as follows:

![](https://docs.google.com/drawings/u/0/d/smk5QaTm4GRrz3cNLhVwb3Q/image?w=336&h=131&rev=143&ac=1&parent=1-uqwpuE6DfwrstfsEuamnxBImv4kiZmxnKbhHRXHLpU)

Each item has instance variables of name and quality, the GildedRose class has an instance variable of all items, and updates the quality of each item daily. The GildedRose updateQuality() method looks as follows:

```java
public class GildedRose {
    Item[] items;

    public GildedRose(Item[] items) {
        this.items = items;
    }

    public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            if (!items[i].name.equals("Aged Brie")
                    && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                if (items[i].quality > 0) {
                    if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].quality = items[i].quality - 1;
                    }
                }
            } else {
                if (items[i].quality < 50) {
                    items[i].quality = items[i].quality + 1;

                    if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }
                }
            }
        }
    }
}
```

When the Gilded Rose Inn adds a new item, we modify the GildedRose updateQuality method to incorporate another if statement reflecting the new item’s quality update behavior. This change could foster errors and result in unpredictable side-effects.

An alternative and more flexible design involves encapsulating the quality update behavior within each item type. See potential design below.

![](https://docs.google.com/drawings/u/0/d/sX1Ku1tnh5SB3krM2BBpbgA/image?w=336&h=265&rev=177&ac=1&parent=1-uqwpuE6DfwrstfsEuamnxBImv4kiZmxnKbhHRXHLpU)

In this design, we encapsulate the update behavior in each item type, which implements the Updateable interface. The Updateable items are passed in as instance variables to Gilded Rose, which calls each items update method. If there is a new requirement for a new item, we extend the code by adding a new item type implementing the Updateable interface. We don’t need to change Gilded Rose, or any of the update methods in other items. See below the new updateQuality method in GildedRose, and an example item’s update method.

```java
public class GildedRose {
    public static ArrayList<Updateable> updateQuality(ArrayList<Updateable> items) {
        items.forEach(Updateable::update);
        return items;
    }
}

public class AgedBrieItem implements Updateable {
    public final String name;
    public int quality;

    public AgedBrieItem(String name, int quality) {
        this.name = name;
        this.quality = quality;
    }
    @Override
    public void update() {
        this.quality = Math.min(50, this.quality + 1);
    }
}

```

This design reduces the chance of bugs and unpredictable side effects. The design principle “Encapsulate what varies” promotes flexible applications, and minimizes the impact of change.
