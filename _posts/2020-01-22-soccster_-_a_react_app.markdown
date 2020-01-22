---
layout: post
title:      "Soccster - a React App"
date:       2020-01-22 19:38:38 +0000
permalink:  soccster_-_a_react_app
---

For my Final Project at Flatiron's Software Engineering Program I built an app that aligned with my passions outside of tech. My application Soccster is built for soccer coaches to be able to find, create, and share drills to use practices.

I accomplished this by creating a drag and drop interface that is basically a virtual coaches board. Building this out came with many challenges. I used a library called Konva to help me. Konva is a library that makes drawing shapes extremely easy. The tricky part however is being able to keep track of where all the shapes the user drags and drops. My solution was to constantly update the state for each of the shapes using onDragEnd, serialize the entire stage to JSON and send the JSON, title, description, and category to the database when the user submits the drill.

Once you finish "drawing" your drill you can give your drill a title, description, and category. The category that you give your drill will be the searchable attribute that other coaches can use to find drills to help them.


