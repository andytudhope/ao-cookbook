# Building a Chatroom in aos

:::warning
This tutorial is actively under development and the content is subject to change.
:::

::: info
If you've found yourself wanting to learn how to create a chatroom within `ao`, then that means we understand at least the basic methodology of sending and receiving messages. If not, it's suggested that you review the [Messaging](messaging) tutorial before proceeding.
:::

In this tutorial, we'll be building a chatroom within `ao` using the Lua scripting language. The chatroom will feature two primary functions:

1. **Register**: Allows processes to join the chatroom.
2. **Broadcast**: Sends messages from one process to all registered participants.

Let's begin by setting up the foundation for our chatroom.

## Step 1: The Foundation

- Open your preferred code editor.

::: info
You may find it helpful to have the [Recommended Extensions](../../references/editor-setup.md) installed in your code editor to enhance your Lua scripting experience.
:::

- Create a new file named `chatroom.lua`.

![Chatroom Lua File](/chatroom1.png)

## Step 2: Creating The Member List

- In `chatroom.lua`, you'll begin by initializing a list to track participants:

  ```lua
  Members = Members or {}
  ```

  ![Chatroom Lua File - Naming the Member List](/chatroom2.png)

  - Save the `chatroom.lua` file

## Step 3: Load the Chatroom into aos

With `chatroom.lua` saved, you'll now load the chatroom into `aos`.

- If you haven't already, start your `aos` in your terminal inside the directory where chatroom.lua is saved
- In the `aos` CLI, type the following script to incorporate your script into the `aos` process:

  ```lua
  .load chatroom.lua
  ```

  ![Loading the Chatroom into aos](/chatroom3.png)

  As the screenshot above shows, you may receive `undefined` as a response. This is expected, but we still want to make sure the file loaded correctly.

  ::: info
  In the Lua Eval environment of aos, when you execute a piece of code that doesn't explicitly return a value, `undefined` is a standard response, indicating that no result was returned. This can be observed when loading resources or executing operations. For instance, executing `X = 1` will yield `undefined` because the statement does not include a return statement.

  However, if you execute `X = 1; return X`, the environment will return the value `1`. This behavior is essential to understand when working within this framework, as it helps clarify the distinction between executing commands that modify state versus those intended to produce a direct output.
  :::

- Type `Members`, or whatever you named your user list, in `aos`. It should return an empty array `{ }`.

  ![Checking the Members List](/chatroom4.png)

  If you see an empty array, then your script has been successfully loaded into `aos`.

## Step 5: Creating Chatroom Functionalities

### The Registration Handler

The register handler will allow processes to join the chatroom.

#### Adding a Register Handler

1. **Modify `chatroom.lua`** to include a handler for `Members` to register to the chatroom with the following code:

   ````lua

   - Modify `chatroom.lua` to include a handler for `Members` to register to the chatroom with the following code:

     ```lua
     Handlers.add(
       "register",
       Handlers.utils.hasMatchingTag("Action", "Register"),
       function (msg)
         table.insert(Members, msg.From)
         Handlers.utils.reply("registered")(msg)
       end
     )
   ````

   ![Register Handler](/chatroom5.png)

   This handler will allow processes to register to the chatroom by responding to the tag `Action = "Register"`. A printed message will confirm stating `registered` will appear when the registration is successful.

2. **Reload and Test:**:
   Let's reload and test the script by registering ourselves to the chatroom.

   - Save and reload the script in aos using `.load chatroom.lua.
   - Check to see if the register handler loaded with the following script:

   ```lua
    Handlers.list
   ```

   ![Checking the Handlers List](/chatroom6.png)

   This will return a list of all the handlers in the chatroom. Since this is most likely your first time developing in `aos`, you should only see one handler with the name `register`.

   - Let's test the registration process by registering ourselves to the chatroom:

   ```lua
    Send({ Target = ao.id, Action = "register" })
   ```

   If successful, you should see that there was a `message added to your outbox` and that you then see a new printed message that says `registered`.

   ![Registering to the Chatroom](/chatroom7.png)

   - Finally, let's check to see if we were successfully added to the `Members` list:

   ```lua
    Members
   ```

   If successful, you'll now see your process ID in the `Members` list.

   ![Checking the Members List](/chatroom8.png)

## Inviting Morpheus to the Chatroom

Now that you've successfully registered yourself to the chatroom, let's invite Morpheus to join us. To do this, we'll send an invite to him that will allow him to register to the chatroom.

Morpheus is an autonomous agent with a handler that will respond to the tag `Action = "Join"`, in which will then have him use your `register` tag to register to the chatroom.

- Let's send Morpheus an invitation to join the chatroom:
  ```lua
  Send({ Target = Morpheus, Action = "Join" })
  ```
- To confirm that Morpheus has joined the chatroom, check the `Members` list:
  ```lua
  Members
  ```
  If successful, you should see Morpheus' process ID in the `Members` list.

## Broadcasting Messages

Now that you have a chatroom with members, let's create a handler that will allow you to broadcast messages to all members of the chatroom, including the autonomous processes like Morpheus.

- Add the following handler to the `chatroom.lua` file:

  ```lua
    Handlers.add(
      "broadcast",
      Handlers.utils.hasMatchingTag("Action", "Broadcast"),
      function (msg)
        for _, recipient in ipairs(Members) do
          ao.send({Target = recipient, Data = msg.Data})
        end
        Handlers.utils.reply("Broadcasted.")(msg)
      end
    )
  ```

  This handler will allow you to broadcast messages to all members of the chatroom.

- Let's test the broadcast handler by sending a message to the chatroom:

  ```lua
    Send({Target = ao.id, Action = "Broadcast", Data = "Broadcasting My 1st Message" })
  ```

  - If successful, you should see that there was a `message added to your outbox` and that you then see a new printed message that says `Broadcasting My 1st Message` because you are also a recipient of this message since you're a member of the `Members` chatroom.

## Engaging Others in the Chatroom

### Onboarding Others

- Invite aos Users:
  Encourage other aos users to join your chatroom. They can register and participate in the broadcast.

- Provide Onboarding Instructions:

  Share a simple script with them for easy onboarding:

```
Hey, let's chat on aos! Join my chatroom by sending this command in your aos environment:
Send({ Target = [Your Process ID], Action = "Register" })
Then, you can broadcast messages using:
Send({Target = [Your Process ID], Action = "Broadcast", Data = "Your Message" })
```

## Next Steps

Congratulations! You've successfully built a chatroom in `ao` and have invited Morpheus to join you. You've also created a broadcast handler to send messages to all members of the chatroom.

Next, you'll continue to engage with Morpheus, but this time you'll be adding Trinity to the conversation. She will lead you through the next set of challenges. Good Luck!