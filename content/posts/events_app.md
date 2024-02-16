---
author: "Abhay Vashist"
title: "Events App"
date: 2024-02-08T13:47:03-06:00
description: "I build a event app using React and FastAPI"
tags: ["FastAPI", "python", "React", "Typescript"]
series: ["Personal Project"]
aliases: ["/events_app"]
weight: 1
draft: false
ShowToc: true
TocOpen: true
cover:
    image: https://img.freepik.com/free-vector/happy-people-dancing-party-flat-illustration_74855-5264.jpg
---

I build Events Web Application using `FastAPI`, `React`, and `SQLite`: [Check it out](https://avashist1998.github.io/events-app/)

Github Repo: [Check it out](https://github.com/Avashist1998/events-app)

## What Technologies I used
- Python (Programming Language)
- FastAPI (Backend Framework)
- Typescript (Programming Language)
- React (Frontend Framework)
- Material UI (Styling)
- Tailwind CSS (Styling)
- Vite (Frontend Bundler)
- SQLite (Database)
- Docker (Containerization)
- CloudFlare (DNS and SSL)
- Github Pages (SPA Hosting)
- Digital Ocean (Backend Hosting)
- Github (Version Tracking)
- Github Action (CI pipeline)

## How did I build It

I implemented the business logic in the FastAPI application. There are a total of four REST endpoints, for four tables making up the application. The database-based used to manage the application data is SQLite. The UI experience was written in Typescript using the react library. The Material UI library was used for icons, and components. The styling was done through the use of tailwind.

## Design and Architecture

### FastAPI

    Routes
        - /users
            /{user_id}
                GET: Get user with that id
            PUT: Update user info
            POST: Create a user
            DELETE: Remove User
        - /events
            /{event_id}
                GET: get Event with that id
            GET: get a list of events
            PUT: Update event
            POST: Create Event
            DELETE: Remove Event
        - /entries
            /{event_id}
                GET: get Entry with that id
            GET: get a list of entries
            PUT: Update Entry
            POST: Create Entry
            DELETE: Remove Entry
        - /auth
            GET: get User Info
            POST: user Log In
            DELETE: user Logs Out

### Database

    Tables
        - users
            user_id: str
            user_name: str
            user_email: str key
            hashed_password: str
            date_created: datetime

        - sessions
            user_id: str
            session_id: str, key
            date_create: datetime

        - entries
            entires_id: str
            event_id: str, key
            date_create: datetime

        - events
            event_id: str, key
            event_name: str
            creator_email: str
            event_public: bool
            event_location: str
            event_date: datetime
            date_create: datetime

### React

    - Pages
        - Login/SignIn Page
        - Events Pages
        - Event Page
        - User Page

    - Forms
        - Sign Up Form
        - Login Form
        - Event Form
        - Entry Form
        - User Form
        - Search Form


### Production Deployment
![](https://raw.githubusercontent.com/Avashist1998/Avashist1998.github.io/main/static/images/events_app_architecture.png)

## Final Thoughts

I struggle a lot when deploying the project to the cloud and dealing with session cookies. I learned about REST route naming, how to work with session cookies, and how to work with a SQLite database. The application provides a simple template for structure monolithic architectures. I also had to design an application that would be mindful of the behavior of different browsers and screen sizes.

