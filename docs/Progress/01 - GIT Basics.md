title: GIT Basics

Imagine you and your friend are building a **photo editor app** together—something that lets people crop images, apply filters, and maybe draw doodles on their pictures. At first, you both just write code and save it on your laptops. You add the cropping feature. Your friend adds the filter feature. But soon, you realize a big problem: how do you **keep track of all the changes**, and how do you **combine your work without messing up the project**?

This is where **Git** becomes incredibly useful.

Git is like a **time machine** for your project. It takes **snapshots** of your code at every important moment. These snapshots are saved in a special folder called a **repository**. You don’t have to rename files or keep lots of copies like `photoeditor_final`, `photoeditor_final_final`, or `photoeditor_really_final_this_time`. Instead, Git remembers every change neatly, so you can go **back in time** to any version whenever you want.

Here’s how it works.

On your **laptop**, you have the project folder. This is called your **working directory**. It’s where you write code, test features, break things, and fix them again. But Git doesn’t save every small change instantly. Instead, you tell Git when a certain version is important—like when a feature works or when something is ready to share. Git then takes a **snapshot** of the entire project at that point and stores it in its internal system.

Now suppose you and your friend both want to work on different parts of the photo editor. You’re working on the cropping tool. Your friend is working on image filters. You both have a **copy** of the project on your own laptops. This copy is called a **clone** of the project repository.

Even though you're working separately, Git keeps track of each person’s changes independently. You don’t need to sit next to each other or wait for one to finish. You both work on your parts of the app.

When you’re done with your changes, you can **send** them to the shared Git repository—usually stored on a central server like GitHub. This is called **pushing** your work. Your friend can do the same with their changes. Git can then **combine** both sets of changes, and if anything overlaps or conflicts, it tells you exactly where the problem is so you can fix it.

This is why Git is so powerful:

- You always have a full **history** of the project. You can look at what changed, when it changed, and even who changed it.
- You can **go back** to older versions if something breaks.
- You and your teammates can work on different parts of the project at the same time.
- You don’t need internet all the time. You can work offline, and Git remembers your changes. Later, when you’re online, you send the updates.

In short, Git helps you **stay organized**, **avoid mistakes**, and **work together smoothly**, especially when building something complex like a photo editor app. It’s like having a smart assistant who watches over your project, remembers everything, and helps you piece it all together, even when you and your team are coding in different places at different times.

