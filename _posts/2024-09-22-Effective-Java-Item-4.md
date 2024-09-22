---
layout: post
title:  "4. Enforce noninstantiability with a private constructor"
date:   2024-09-22 19:09:00 +0900
categories: EffectiveJava
---

Some classes, like utility classes, aren’t designed to be instantiated. However, it's not uncommon to see instances of such classes.

Making a class `abstract` doesn't prevent instantiation, as subclasses can still be instantiated. Additionally, it can mislead users into thinking the class is meant to be inherited.

The effective way to prevent instantiation is by making the constructor `private`. It's recommended to include a comment explaining the reason for this, to provide clarity. This approach also prevents inheritance, as subclasses cannot access the superclass's private constructor.