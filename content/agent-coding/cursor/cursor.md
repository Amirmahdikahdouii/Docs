---
title: "Cursor Agent: 10 Pro Tips"
weight: 1
---
# Cursor Agent: 10 Pro Tips

This document is based on [Cursor Agent: 10 Pro Tips!](https://youtu.be/WVeYLlKOWc0?si=prsUKj83Blro3JU2)

1. Plan mode
2. Context menu: For example running a new chat using `@` symbol, and add context to the agent to compare the changes with main branch, and see if there is an issue to find and solve.
3. Custom commands:
Inside `.cursor` folder, you can add a custom command, which is a **markdown file**, that will be used by `/custom_command_name` to easily run the custom prompt as easy as possible. For example having a custom `/docs` command would be helpful, to generate a document using a defined template.
4. Images: Cursor allows you to give images as a context to the agent, to implement features based on your needs.
5. Duplicate chats:
Imagine that you have a chat, which you want to try some changes using the chat context, but you already want to keep your chat as well. Duplicate chats allows you to implement and test your ideas, without breaking the corrent context
6. Context visibility:
The contexts are important in chats, they are the brain of the agent, if their grow, every prompt will spend more value from your api limitations, result in finish limits of plan earlier.
Also there is a `/summarize` command, which helps you to summerize corrent context for agent, to free space for new contexts, and also have cheaper commands

> [!TIP]
> Pro tip: for summerize, it's recommanded to read the summery first, then if there are some missed data, append it to the summary then apply it to the agent.

7. Usage visibility:

In cursor settings -> Agents -> usage summary: you can change this settings and set to `always` to always show you the usage of your plan in the editor UI.

8. Keyboard shortcuts
9. New chats:

Chatting and implementing a lot of features in a single chat is a bad option, which over time and by more usage, the model efficiency will decrease.
The recommandation is to use new chats for apply new changes in code base.

10. Checkpoints:

You can rollback changes in chat, but git is recommended!
