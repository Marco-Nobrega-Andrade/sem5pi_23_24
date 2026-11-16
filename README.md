<div align="center">

# RobDroneGo

A web platform for the robots and drones that patrol the ISEP campus and carry things between rooms. Managers map the campus and the fleet, people ask for a task, and the system works out the route and the order each robot should do its work in.

![Angular](https://img.shields.io/badge/Angular-16-DD0031?style=for-the-badge&logo=angular&logoColor=white)
![Three.js](https://img.shields.io/badge/Three.js-3D-000000?style=for-the-badge&logo=threedotjs&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-TypeScript-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![.NET](https://img.shields.io/badge/.NET-8-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)
![Prolog](https://img.shields.io/badge/Prolog-planning-74283C?style=for-the-badge&logoColor=white)

[Objectives](#objectives) · [Features](#features) · [Architecture](#architecture) · [Planning](#planning) · [Team](#team)

<sub>5th-semester integrative project · Informatics Engineering · <a href="https://www.isep.ipp.pt">ISEP</a>, Porto · 2023–24</sub>

</div>


## Objectives

The brief imagined ISEP asking for one system to run a fleet of robots and drones on its campus. These were the goals:

- [x] Keep a model of the campus (buildings, floors, lifts, rooms and the passages between buildings) and of the fleet, with each robot type limited to the tasks it can do
- [x] Let campus users ask for surveillance or pick-up and delivery tasks, and let a task manager approve them and plan who does what and in which order
- [x] Show any floor in 3D in the browser and animate a robot following its planned route
- [x] Work as a team of four over three Scrum sprints, with requirements, domain model, architecture views, design and tests for each user story
- [x] Bring four courses together in one project: web architecture (ARQSI), algorithms and Prolog (ALGAV), computer graphics (SGRAI) and systems administration (ASIST), plus GDPR rules for the users' personal data


## Features

Everyone logs in and gets the menu for their role.

| | Role | What they can do |
|:-:| --- | --- |
| 🛠️ | **Administrator** | Create accounts with a role, approve or reject sign-up requests from campus users |
| 🏢 | **Campus manager** | Create, edit and list buildings, floors, lifts, rooms and passages; list buildings by number of floors; upload each floor's map; open the 3D view |
| 🤖 | **Fleet manager** | Create and delete robot types and the tasks each one can do, add robots, list the fleet, take a robot out of service |
| 🗺️ | **Task manager** | Find the route between two buildings, list pending requests, approve or reject them, plan the order of each robot's tasks, delete tasks |
| 🙋 | **Campus user** | Sign up after accepting the privacy policy, ask for a surveillance or pick-up and delivery task, change their details, export their personal data, delete their account |

## Architecture

The platform is split into separate services, each with its own job:

```
Angular SPA  →  Auth service (Node.js)
             →  Campus and fleet API (Node.js)  →  Planning server (Prolog)
             →  Tasks API (.NET)                →  Planning server (Prolog)
```

The Angular app is the only thing users see, and it also holds the Three.js 3D view. The auth service handles accounts and logins and hands out JWT tokens that the other services check. The campus and fleet API, in TypeScript with Express, holds buildings, floors, maps, robot types and robots. The tasks API, in C# on .NET 8, holds the task requests and their state. Both APIs follow the same layers (routes or controllers, services, domain, repositories) and store their data in MongoDB. The planning server, written in Prolog, does the route and scheduling work for both APIs over HTTP.

We kept tasks in their own service so they could be written in a different language and grow on their own, and kept the heavy search work in Prolog, where it was easiest to express. The services were tested with Jest, Mocha, Sinon, xUnit and Postman, the frontend with Karma and Cypress (close to 1,000 tests in all), and GitHub Actions and Azure Pipelines built and deployed the app to Azure.

## Planning

Each floor's map is a grid, and every cell is either free or a wall. To get from one room to another, the planning server first works out which buildings and floors to go through, with a depth-first search over the passages between buildings and the lifts inside them. Then it finds the path across each floor with A*, with or without diagonal moves.

When a robot has several tasks, the order matters, because each task ends somewhere and the next one starts somewhere else. For a few tasks the server tries every possible order and keeps the fastest. That grows very fast, so for longer lists it uses a genetic algorithm instead: it starts with random orders, keeps the best ones, mixes them and adds small random changes over many generations. We timed the grid searches and the exact ordering on growing inputs to see where each one stops being practical.

The 3D view builds each floor from its map, with walls, doors and lifts that open, and moves a robot along the path the planner returned.

## Project structure

```
SPA/              Angular frontend and Three.js 3D view, Karma and Cypress tests
auth/             accounts and login service (Node.js + TypeScript)
MDRoboISEP/       campus and fleet API (Node.js + TypeScript), Jest and Mocha tests, Postman collections
MDTarefas/        tasks API (C# on .NET 8)
MDTarefasTest/    xUnit tests and Postman collections for the tasks API
Planeamento/      Prolog planning server: routes, A* and task ordering
docs/             domain model, architecture views, requirements and design per sprint (A–C), GDPR and sysadmin reports
```

> The app's screens and the docs are written in Portuguese.

## Team

<table align="center">
  <tr>
    <td align="center" width="150">
      <a href="https://github.com/Marco-Nobrega-Andrade"><img src="https://github.com/Marco-Nobrega-Andrade.png?size=200" width="100" alt="Marco Andrade"/></a><br>
      <b>Marco Andrade</b><br>
      <a href="https://github.com/Marco-Nobrega-Andrade">@Marco-Nobrega-Andrade</a>
      <br><br>
      <a href="https://github.com/Joao-Batista-Isep"><img src="https://github.com/Joao-Batista-Isep.png?size=200" width="100" alt="João Batista"/></a><br>
      <b>João Batista</b><br>
      <a href="https://github.com/Joao-Batista-Isep">@Joao-Batista-Isep</a>
    </td>
    <td align="center" width="150">
      <a href="https://github.com/SrMasterAD"><img src="https://github.com/SrMasterAD.png?size=200" width="100" alt="David Dias"/></a><br>
      <b>David Dias</b><br>
      <a href="https://github.com/SrMasterAD">@SrMasterAD</a>
      <br><br>
      <a href="https://github.com/EzequielEstima-ISEP"><img src="https://github.com/EzequielEstima-ISEP.png?size=200" width="100" alt="Ezequiel Estima"/></a><br>
      <b>Ezequiel Estima</b><br>
      <a href="https://github.com/EzequielEstima-ISEP">@EzequielEstima-ISEP</a>
    </td>
  </tr>
</table>