# Pros and Cons of Hybrid vs. Native Apps


### How does this technique with Famo.us compare to other app-building methods?

The short answer: Using Famo.us for laying out and displaying your app is incredibly easy and enables a novice developer to create native-like (in terms of responsiveness and the "feel" of using the app) apps. 

The long answer: It depends on your needs. If you're a team of Obj-C wizards who don't know a lick of Javascript, you'll probably be better served sticking to your guns. But, if you've been building front ends for websites in Javascript/HTML/CSS for any length of time, this technique can help you quickly create an app that would convince somebody into thinking you've built the app using the native toolchain on the platform (Obj-C, Java, C#), in respect to how it looks and feels through transitions and touch feedback.


### Breaking down the Pros and Cons of Hybrid. Vs. Native 

Again, each company/app is different and has unique requirements that may prioritize one implementation above another. These pros/cons are from the perspective of a small development team (or a single programmer) with limited resources: 

- time of development
- performance for the end-user 
- maintainability of code


#### Time of development 

Hybrid apps are perfectly suited for quickly prototyping applications. Adding/removing routes and pages can be done quickly and easily, and as you finalize designs, each page can be improved to fit into your scheme. Debugging these applications is also familiar to most web developers; just use Chrome's Dev Tools! 


#### Performance for the end-user 

This is arguably of greatest concern, because it affects IF user's will use your app. A half-broken, visually janky app is not going to end up on anyone's Home screen. 

When using Famo.us, the performance of a production app has the capability to equal, and surpass, a native app. This is in respect to the way the app "feels" when moving about, and how it looks as you do so. From handling multi-touch gestures, to intricately animating the Header and Footer of each page, Famo.us is able to maintain 30 frames-per-second on even older iOS and Android devices. 

But not everything is roses and honey for hybrid performance. There absolutely are areas where native apps can achieve significant performance increases compared to Famo.us, such as when handling large amounts of data while also animating. 


#### Maintainability of code  

Further down the line in the thought/build process is the question of Who Will Maintain This? While high-quality native iOS and Android developers may be difficult to find (an unemployed, great iOS developer is a "unicorn"), the hybrid approach opens your code up to millions of quality web developers. Knowledge of Famo.us is hardly a prerequisite - any Javascript developer who can use jQuery should be proficient in Famo.us within two weeks. 
