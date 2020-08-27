---
layout: post
title:      "My JavaScript Project - Quick Polls"
date:       2020-08-27 13:03:05 +0000
permalink:  my_javascript_project_-_quick_polls
---


As my JavaScript portfolio project, I built a basic voting SPA where users can create polls, vote on polls that they are authorized to see, and visualize the results of the polls. Users also have the ability to add other users to their friend list so that they can share polls with them. 

The main features of the application: 

* The app renders the actual content only after successful user authentication.
* The initial "home" page shows aggregated data on the pending and closed polls the user has been added to or created.
* By clicking on the Pending Polls menu item, the user can see all his/her pending polls with the current results
as a chart and with the voting form displaying all the options users can vote for.
* Upon voting or removing a vote, the app changes the displayed percentage data in the DOM accordingly, beside modifying the data in the database.
* Users can create polls and set the question and the options along with optional voting requirement (number of votes) or voting period (number of days) in order to close the poll automatically when the given condition is met.
*  Users can also add friends to the polls from their friend list so the added friends can vote as well.
*  Users can edit, close or delete the polls that they created.
*  After clicking on the "Friends" menu item, users can search for users by username and add users to their friend list so that they can add the particular friend to a poll. Also, a current friend can be removed from the friend list.


The project walkthrough video is available via this [link](https://drive.google.com/file/d/10PnH3t3M3yuZP8dkzbIiLfbIHXCmiMyb/view?usp=sharing) and if you would like to try out the application in your browser or contribute to the code, you can find the repository here on [GitHub](https://github.com/dpataki92/quick-polls-api).
