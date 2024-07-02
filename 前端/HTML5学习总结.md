[TOC]

#HTML5学习总结

##没有闭合标签的元素

- img

  ```html
  <img src="http://www.baidu.com/1.jpg">
  ```

  

- input

  ```html
  <input type="text" placeholder="cat photo name" required>
  ```

  

## 优先级

1. 元素有多个class 有冲突的话以后面的class为准

   ```html
   <style>
     .class1 {
       color: red;
     }
     .class2 {
       color: green;
     }
     .class3 {
       color: white;
     }
   </style>
   <p class="class1 class2 class3"></p>
   ```

   以最后面的class3选择器为准

2. id选择器优先高于class选择器

   ```html
   <style>
     .class1 {
       color: red;
     }
     .class2 {
       color: green;
     }
     .class3 {
       color: white;
     }
     #my {
       color: pink;
     }
   </style>
   <p id="my" class="class1 class2 class3"></p>
   ```

   id选择器优先

3. 内联样式优先级高于id选择器
	```html
   <style>
     .class1 {
       color: red;
     }
     .class2 {
       color: green;
     }
     .class3 {
       color: white;
     }
     #my {
       color: pink;
     }
   </style>
   <p id="my" class="class1 class2 class3" style="color: black;"></p>
	```
4. Important的优先级最高 
	```html
   <style>
     .class1 {
       color: red;
     }
     .class2 {
       color: green;
     }
     .class3 {
       color: white;
     }
     #my {
       color: pink !important;
     }
   </style>
   <p id="my" class="class1 class2 class3" style="color: black;"></p>
	```
   加了!important后此优先级最高

## CSS选择器类型

- ID选择器

  通过**#id**的方式来选择

- class选择器

  通过**.classname**的方式来选择

- 属性选择器

  通过**[attr=value]**的方式来选择