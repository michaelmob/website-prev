---
title: System-wide Vim/Emacs Movement Keys
description:
toc: true
authors:
  - michaelmob
tags:
  - emoji
categories:
series:
date: '2020-08-29'
lastmod: '2020-08-29'
draft: true
---

system-wide vim keys post

## A little bit of theory...

Nestled between the num-pad and the alpha-numeric section of the keyboard lies
some special keys that are sometimes referred to as "cursor control keys".  They
serve the purpose of controlling the **view** or **cursor**.

### What the **view** is
<!-- <video src="example-of-view.webm" alt="Visual example of view definition" autoplay></video> -->

The **view** contains all the content and the **cursor**. Scrolling is an action
that moves the **view**.

### What the **cursor** is
<video src="example-of-cursor.webm" alt="Visual example of cursor definition" autoplay loop></video>

The **cursor** is the blinking line you see in a text field. It lets you know
that the position it's at is where new input will be placed.

All of the actions of moving the view and the cursor are controlled by the control keys.

### The Control Keys
![hi](control-keys.png)

The arrow keys are too far away on a full-sized 104-key keyboard. They're too
far away from my mouse and they're too far away from the home row. I'm not here
to explain why, I'm here to complain and give a poorly thought-out solution.

Written by a right-handed person.

### The Problem from the Home Row

I'm not a fan of taking my hands off my keyboard. I can take that further by
saying that I'm not a fan of taking my fingers off the home row. I groan each
time that I have to take my right hand off the home row to navigate a menu with
my directional keys. My uncomfortable and awkward reaching movements to hit the
down arrow with my pinky is not doing any favors for my wrist.

The access to the control keys requires a relatively large hand movement.


## A solution

Using a custom keyboard symbols map, we are able to extend the English (US)
keymap with some niceties.

### Create Symbols File

Create new file `/usr/share/X11/xkb/symbols/vi` with our symbols.
<!-- (Don't actually do this, click here to scroll to the patch) -->
{{< gist michaelmob dfff1608af185ca31af767dfa393645a vi >}}


### Create Rule Files

Four more rules files must be edited namely:
- `/usr/share/X11/xkb/rules/base.lst`
- `/usr/share/X11/xkb/rules/base.xml`
- `/usr/share/X11/xkb/rules/evdev.lst`
- `/usr/share/X11/xkb/rules/evdev.xml`

The `xml` files must be identical to eachother and the `lst` files must be
identical to eachother. These can *probably* be symlinked.


### Update the XML rules

In the `xml` files, we need to add a `<layout/>` element to `<layoutList/>` in
both the base and evdev files.

```xml
<!-- /usr/share/X11/xkb/rules/{base,evdev}.xml
     ... -->
<layoutList>
  <!-- START; add the below element to layoutList -->
  <layout>
    <configItem>
      <name>vi</name>
      <shortDescription>vi</shortDescription>
      <description>English (vi)</description>
      <languageList><iso639Id>eng</iso639Id></languageList>
    </configItem>
  </layout>
  <!-- END -->
</layoutList>
```

### Update the `lst` rules

In the `lst` files, we need to define our new keyboard layout.
These are a little less complicated.

Each section is represented with an exclamation mark!

In the `/usr/share/X11/xkb/rules/{base,evdev}.lst` files, we need to add our new
keyboard layout.

Find the `! layout` section and add our new layout:
```
  vi              English (vi)
```


## Patches

Luckily, we can use patches to do this for us.


### XML Patch
The following patch is to make life easier by patching the XML rules files.

{{< gist michaelmob dfff1608af185ca31af767dfa393645a xkb-xml.patch >}}

Aside: You can spot a little nugget of how the diff algorithm calculated this
patch. The `<layout>` and `<configItem>` lines are the same above and below the
`<configItem>`'s content. You can see that the diff algorithm found it more
efficient to match to the bottom-most tag pair.

### `lst` Patch
{{< gist michaelmob dfff1608af185ca31af767dfa393645a xkb-lst.patch >}}
