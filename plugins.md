---
description: Here you'll find documentation on Bastion's Plugin System.
---

# Plugins

Bastion v8 was [recently released](https://notes.traction.one/posts/6674640902554005511). With v8, we introduced the **Plugin System**. This enables users to add plugins to their instance of Bastion and extend the default features of Bastion.

This documentation documents how to create plugins for Bastion so that developers can create amazing plugins for Bastion that can be used by anyone hosting their own instance of Bastion.

{% hint style="info" %}
This documentation is updated when there's any change to the internals of Bastion. So, keep referring this doc for updated info about Bastion Plugins. If you've any issues or questions, feel free to ask them in [Bastion HQ](https://discord.gg/fzx8fkt).
{% endhint %}

## Get Started

We have made the plugin development process easier for anyone who's looking to create a plugin for Bastion. We've set up a [template repository in GitHub](https://github.com/TheBastionBot/bastion-example-plugin), which you can use to bootstrap your plugin development.

#### Step 1 - Clone the Plugin Template

![](.gitbook/assets/screenshot-from-2020-05-20-17-49-26.png)

1. Go to the [template repository](https://github.com/TheBastionBot/bastion-example-plugin) on GitHub: [https://github.com/TheBastionBot/bastion-example-plugin](https://github.com/TheBastionBot/bastion-example-plugin)
2. Click on [**Use this template**](https://github.com/TheBastionBot/bastion-example-plugin/generate).
3. Enter the repository details \(see Plugin Guidelines for naming convention\), and then your plugin repository will be created.

#### Step 2 - Go Make Cool Stuff!

Now, go through the rest of the guide to see how to actually code the plugin and then your plugin is ready to be published for users to install it in their instance of Bastion.

We can't wait to see what you make.

## Plugin Types

Bastion currently allows plugins for these internal modules:

* **Commands**
* Interrupts
* **Listeners**
* Monitors
* **Schedulers**

We'll go through each of these modules and see what they do and how we can create a plugin for that module.

{% hint style="info" %}
We'll follow the rest of the guide assuming you've setup your repository structure similar to our template repository.
{% endhint %}

### Commands Plugin

Command plugins are used to add additional commands to Bastion. There're basically three things you need to know before you can start creating command plugins.

* [x] All the commands in your plugin should go inside the `commands` directory.
* [x] Command file names should be camelCased and should be exactly the same as the command name.
* [x] Command Class names should be TitleCased and should use the suffix `CommandPlugin`. e.g. if your  command is `jedi`, The command class should be `JediCommandPlugin`.

#### Command Configurations

The command configuration is done in the constructor of the command class by calling the `super` class constructor with the command name and command configurations as the arguments. You can see an example configuration in your repository in the `example` command, if you used our template to create your repository.

Here, we'll see what all these configurations mean and what kind of data they take.

```typescript
// The description of the command.
description?: string;
// The aliases for the command that can trigger this command.
triggers?: string[];
// Arguments configuration options for the arguments accepted by the command.
arguments?: ArgumentParserOptions;
// Execution scope of the command. Channel types where the command can be executed.
scope?: "guild" | "dm";
// Whether or not this command is only executable by the Bastion bot owner(s).
owner?: boolean;
// Whether or not to show 'typing...' status during command execution.
typing?: boolean;
// Whether the command is schedulable.
schedulable?: boolean;
// Whether the command is an unsafe command.
unsafe?: boolean;
// Whether the command requires to be run in an NSFW channel.
nsfw?: boolean;
// The cooldown period for the command (in seconds).
cooldown?: number;
// The number of times this command can be executed in the cooldown period.
ratelimit?: number;
// Permissions required by Bastion to run this command.
clientPermissions?: PermissionResolvable[];
// Permissions required by the users to run this command.
userPermissions?: PermissionResolvable[];
// The various syntax of the command that's shown in command's help message.
syntax?: string[];
// Any pre-run checks for the command. The command will execute only if this returns true.
condition?: Function;
```

#### Command Logic

The logic for the command is what gets executed when the command is triggered by a user and all the condition that are defined for that command satisfies. If you see the `example` command in the repository, there's an `exec` method in the command class. Your command's logic goes inside this method.

Here's the signature of the `exec` method.

```typescript
/**
 * @params {Message} message - discord.js Message object. Refer https://discord.js.org/#/docs/main/stable/class/Message
 * @params {ParsedArguments} argv - the parsed arguments in key value format 
 */
public async exec(message: Message, argv: CommandArguments): Promise<unknown>;
```

### Interrupts Plugin

\# TODO

### Listeners Plugin

Listener plugins are used to add additional listeners to Bastion that'll listen to native Discord events. There're basically three things you need to know before you can start creating listener plugins.

* [x] All the listeners in your plugin should go inside the `listeners` directory.
* [x] Listener file names should be camelCased.
* [x] Listener Class names should be TitleCased and should use the suffix `ListenerPlugin`. e.g. if your  listener is `gravitationalWave`, The listener class should be `GravitationalWaveListenerPlugin`.

#### Supported Events

You can only create listeners for events that are received from Discord. Well, technically, you can create others too, but it won't get executed as there would be no emitter for it.

To get a list of supported events, you can go [here](https://discord.js.org/#/docs/main/stable/class/Client).

#### Listener Logic

The logic for the listener is what gets executed when the event is emitted. If you see the `example` listener in the repository, there's an `exec` method in the listener class. Your listener's logic goes inside this method.

Here's the signature of the `exec` method.

```typescript
/**
 * Refer the discord.js documentation for the parameters
 * of the event listener you're implementing.
 * @ref https://discord.js.org/#/docs/main/stable/class/Client
 */
public async exec(...args: unknown[]): Promise<unknown>;
```

### Monitors Plugin

\# TODO

### Schedulers Plugin

Scheduler plugins are used to add additional schedulers to Bastion that'll get executed at the scheduled time - based on `cron` expression. There're basically four things you need to know before you can start creating scheduler plugins.

* [x] All the schedulers in your plugin should go inside the `schedulers` directory.
* [x] Scheduler file names should be camelCased and should be exactly the same as the scheduler name.
* [x] Scheduler Class names should be TitleCased and should use the suffix `SchedulerPlugin`. e.g. if your scheduler is `birthday`, The scheduler class should be `BirthdaySchedulerPlugin`.

#### Scheduler Configurations

The scheduler configuration is done in the constructor of the scheduler class by calling the `super` class constructor with the scheduler name and scheduler configurations as the arguments. You can see an example configuration in your repository in the `example` scheduler, if you used our template to create your repository.

Here, we'll see what all these configurations mean and what kind of data they take.

```typescript
// A valid 6 point cron expression - * * * * * *
// Format:
//   Second         0-59 or * or range
//   Minute         0-59 or * or range
//   Hour           0-23 or * or range
//   Day of month   1-31 or * or range
//   Month          1-12 or * or range
//   Day of week    0-7 (0 or 7 is SUN) or * or range
cronTime: string;
```

#### Scheduler Logic

The logic for the scheduler is what gets executed when the cron time is satisfied. If you see the `example` scheduler in the repository, there's an `exec` method in the scheduler class. Your scheduler's logic goes inside this method.

Here's the signature of the `exec` method.

```typescript
/**
 * @returns {void}
 */
public async exec(): Promise<void>;
```

## Verified Plugins & Developers

We'll be having a **Plugin Store** where developers can publish their plugins and where users can search for their desired plugins. And we'll be having a verification program that'll qualify developers as **Verified Plugin Developers** who create plugins of high value and standard. So that when users look at their plugin they'll know they're getting something of quality.

For plugins and developers to be verified, we need to have some basic guidelines. Let's go through the checklist so that we do things the right way. Which will eventually be helpful in the verification process. Note that these guidelines are there just to maintain consistency across various plugins from different developers, and are not mandatory in any way. But we do recommend following this so that the **Plugin Manager** can be used to install your plugin in an instance of Bastion \(yeah, another upcoming feature\).

### Plugin Guidelines

* [x] Repository name should be in this format - `bastion-PLUGIN-NAME-plugin` - replace `PLUGIN-NAME` with the name of your plugin \(lowercase\).
* [x] Directory structure of your plugin should be exactly the same as [our template repository](https://github.com/TheBastionBot/bastion-example-plugin). 
* [x] A plugin should do one specific task. It shouldn't be some "multi-purpose" plugin. Let's face it, no one likes bloatware. Users shouldn't have to install a plugin that does 5 different things when they only need it to do the one thing they require. One plugin, one task - as simple as that.
* [x] Plugin names shouldn't conflict with the existing names in their respective modules.

