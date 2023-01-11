# Mocy-Giveaways

Advance discord giveaways system with Support Slash/Message support

## Features

-   ⏱️ Easy to use!
-   🔄 Automatic restart after bot crash!
-   🇫🇷 Support for translations: adapt the strings for your own language!
-   📁 Support for all databases! (default is json)
-   ⚙️ Very customizable! (prize, duration, winners...)
-   🚀 Super powerful: start, edit, reroll, end, delete and pause giveaways!
-   💥 Events: GiveawayReady, GiveawayStarted, GiveawayWinner, GiveawayRerolled, NoWinner, InvalidGiveaway, UserJoinGiveaway, UserLeftGiveaway
-   🕸️ Support for shards! (Coming Soon...)
-   and much more!

# Installation

```cli
npm i git+https://github.com/JohnDavid30/Mocy-Giveaways
------ or ---------------------
yarn add git+https://github.com/JohnDavid30/Mocy-Giveaways
```

# Setup Giveaway

### Launch of the module

Required Discord Intents: `Guilds` and `GuildMessageReactions`.  
Optional Discord Privileged Intent for better performance: `GuildMembers`.

```js
const GiveawaySystem = require('mocy-giveaways');
const Discord = require('discord.js');
const client = new Discord.Client({
    intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.GuildMembers,
    GatewayIntentBits.MessageContent,
  ],
});

// Requires Manager from mocy-giveaways
// for custom embed
// class CustomManager extends GiveawaySystem {
//   GiveawayStartEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Started`);
//     return embed;
//   }
//   GiveawayEndNoWinnerEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Ended No Winner`);
//     return embed;
//   }
//   GiveawayEndWinnerEmbed(giveaway) {
//     let embed = new EmbedBuilder().setTitle(`Giveway Ended Winners`);
//     return embed;
//   }
// }

// const manager = new CustomManager(client, {
//   embedColor: "Random",
//   pingEveryone: true,
//   emoji: "🎉",
//   description: "Enter",
// });

const manager = new GiveawaySystem(client, {
  embedColor: "Random",
  pingEveryone: true,
  emoji: "🎉", //emoji button
  description: "Enter", //name button
});
// We now have a giveawaysManager property to access the manager everywhere!
client.giveawaysManager = manager;

client.on('ready', () => {
    console.log('Bot is ready!');
});

client.login(process.env.DISCORD_BOT_TOKEN);
```
After that, giveaways that are not yet completed will start to be updated again and new giveaways can be started.
You can pass an options object to customize the giveaways. Here is a list of them:

-   **client**: the discord client (your discord bot instance).
### Start a giveaway

```js
client.on('interactionCreate', (interaction) => {

    if (interaction.isChatInputCommand() && interaction.commandName === 'start') {
        // /start 2d 1 Awesome prize!
        // Will create a giveaway with a duration of two days, with one winner and the prize will be "Awesome prize!"

        const duration = interaction.options.getString('duration');
        const winnerCount = interaction.options.getInteger('winners');
        const prize = interaction.options.getString('prize');

        client.giveawaysManager
            .start(interaction, {
              channel: channel,
              duration: duration,
              prize: prize,
              winnerCount: winnerCount,
              hostedby: interaction.member.id
            })
            .then((data) => {
                console.log(data); // {...} (messageId, end date and more)
            });
        // And the giveaway has started!
    }
});
```

-   **options.duration**: the giveaway duration.
-   **options.prize**: the giveaway prize.
-   **options.winnerCount**: the number of giveaway winners.
-   **options.channel**: the giveaway channel.
-   **options.hostedby**: the owner hosting of giveaway.

This allows you to start a new giveaway. Once the `start()` function is called, the giveaway starts, and you only have to observe the result, the package does the rest!

<a href="https://zupimages.net/viewer.php?id=23/01/9hna.jpg">
    <img src="https://zupimages.net/up/23/01/9hna.jpg"/>
</a>
<a href="https://zupimages.net/viewer.php?id=23/01/dxlv.jpg">
    <img src="https://zupimages.net/up/23/01/dxlv.jpg"/>
</a>

#### ⚠ ATTENTION!

The command examples below (reroll, edit, delete, end) can be executed on any server your bot is a member of if a person has the `prize` or the `messageId` of a giveaway. To prevent abuse we recommend to check if the `prize` or the `messageId` that was provided by the command user is for a giveaway on the same server, if it is not, then cancel the command execution.
### Reroll a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'reroll') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .getWinner(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway rerolled!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```
### Edit a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'edit') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .edit(messageId, {
                winCount: 3,
                prize: 'New Prize!'
            })
            .then(() => {
                interaction.reply('Success! Giveaway updated!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **options.winCount**: the new number of winners.
-   **options.prize**: the new prize.
### Delete a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'delete') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .delete(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway deleted!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **doNotDeleteMessage**: whether the giveaway message shouldn't be deleted.

⚠️ **Note**: when you use the delete function, the giveaway data and the message of the giveaway are deleted (by default). You cannot restore a giveaway once you have deleted it!

### End a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isChatInputCommand() && interaction.commandName === 'end') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager
            .getWinner(messageId)
            .then(() => {
                interaction.reply('Success! Giveaway ended!');
            })
            .catch((err) => {
                interaction.reply(`An error has occurred, please check and try again.\n\`${err}\``);
            });
    }
});
```

-   **noWinnerMessage**: Sent in the channel if there is no valid winner for the giveaway.
## Manager Events

```js
client.giveawaysManager.on("GiveawayReady", (name) => {
  console.log(`${name} is Ready`);
});
client.giveawaysManager.on("GiveawayStarted", (message, giveaway) => {
  // console.log("GiveawayStarted");
  message.reply(`Giveaway Started`);
});
client.giveawaysManager.on("GiveawayWinner", (message, giveaway) => {
  // console.log("GiveawayWinner");
  let Gwinners = giveaway.winners.map((winner) => `<@${winner.userID}>`);
  message.channel.send(
    `${Gwinners} Won The \`${giveaway.prize}\` Giveaway Prize. Hosted By <@${giveaway.hostedBy}>`
  );

  giveaway.winners.map(async (user) => {
    const u = await message.guild.members.fetch(user.userID);
    u.send(`You Won The Giveaway ${message.url}`);
  });
});
client.giveawaysManager.on("GiveawayRerolled", (message, giveaway) => {
  // console.log("GiveawayRerolled");
  message.reply(`\`${giveaway.prize}\` Giveaway Rerolled`);
});
client.giveawaysManager.on("NoWinner", (message, giveaway) => {
  message.reply(`No One Won ${giveaway.prize}`);
});
client.giveawaysManager.on("InvalidGiveaway", (member, giveaway) => {
  member.send(`You are Joining in Ended Giveaway`);
});
client.giveawaysManager.on("UserJoinGiveaway", (member, giveaway) => {
  member.send(`You Joined ${giveaway.prize} Giveaway`);
});
client.giveawaysManager.on("UserLeftGiveaway", (member, giveaway) => {
  member.send(`You Left ${giveaway.prize} Giveaway`);
});
```
# Bugs, glitches and issues

If you encounter any problems feel free to open an issue in our <a href="https://github.com/JohnDavid30/mocy-giveaways/issues">GitHub repository</a> or join the [Discord server](https://discord.gg/rKtCW2S63Y).
