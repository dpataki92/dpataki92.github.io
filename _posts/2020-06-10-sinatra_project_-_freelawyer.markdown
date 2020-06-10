---
layout: post
title:      "Sinatra Project - FreeLawyer "
date:       2020-06-10 08:11:39 +0000
permalink:  sinatra_project_-_freelawyer
---


As my Sinatra portfolio project, I built the basic, 1.0 version of a legal collaboration platform that I intend to significantly improve and extend in the future. Currently, users seeking legal help in the European Union (EU) can sign up as clients and users having EU law experience can sign up as lawyers on the page. Clients can ask legal questions from lawyers, while Lawyers can provide legal help and get upvotes for their answers from clients. 

The application has the following main features for the users:
* All users can view all the questions asked by clients and sort them based on jurisdiction and legal area
* All users can go to an individual question’s page and check out the question’s detailed description along with the answers provided by lawyers
* Clients can upvote the answers they found the most helpful and the answers are sorted based on the number of upvotes associated
* Clients can ask a new question and edit or delete it later (but they can’t answer a question)
* Lawyers can provide answers to questions and delete their own questions (but they can’t ask questions).
* All users can view a list of all lawyers with links to their profile pages where the ranking of the lawyers is based on the number of upvotes they have
* All users can log out or even delete their profile if they are done using the application

The page uses client-side and server-side validation for user input. Users can’t sign up or log in with missing or incorrect data and they can’t use a username or email address that is already used by another user. Users can’t modify other users' resources and the application always checks if the given user is a client or a lawyer and provides the slightly different functionalities and views appropriately.

The [project walkthrough video](https://drive.google.com/file/d/1rn3w1olmF-5qf_eWy1zRD3qytPspNGfH/view?usp=sharing) is available via this link and if you would like to try out the application in your browser or contribute to the code, you can find the repository here on [GitHub](https://github.com/dpataki92/FreeLawyer).


