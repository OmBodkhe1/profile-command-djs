# Badge System

This guide will explain you how to create a Badge System (Profile Command) for your Discord.js Bot.



## Get started

Lets get started by installing some dependencies, open your favourite terminal.
Run the following Commands in your Terminal.

```shell
npm install discord.js@latest
npm install mongoose
```


## Schema

```js
const { Schema, model } = require('mongoose');

let profile = new Schema({
    id: { type: String },
    badges: { type: Array }
})
module.exports = model('Profile Badges', profile);
```


## Profile Command

```js
const { MessageEmbed, CommandInteraction, Client, Message } = require('discord.js');
const profile = require('your schema path')
const config = require('your config path');

module.exports = {
    name: 'profile',
    description: 'check badges of member',
    userPrams: [],
    botPrams: ['EMBED_LINKS'],
    options: [
        {
            name: "user",
            description: 'check badges of member',
            type: "USER",
            required: false
        }
    ],

    /**
     *
     * @param {Client} client
     * @param {CommandInteraction} interaction
     */

    run: async (client, interaction) => {

        const option = interaction.options.getUser('user')
        let mem
        if (option) mem = option
        else mem = interaction.member.user
        const member = interaction.client.users.cache.get(mem.id)
        const data = await profile.findOne({ id: member.id })
        if (!data) {
            await profile.create({ id: member.id })
            return interaction.reply({
                embeds: [
                    new MessageEmbed()
                        .setColor(config.embedColor)
                        .setAuthor({ name: 'Profile' })
                        .setDescription(`Name: **${member.tag}**\n Id: **${member.id}**`)
                        .addField("__Astroz Achievements__", `You not have any badges click [here](https://discord.gg/astrozmusic) to buy premium of **${client.user.tag}** to get badges!`)
                        .setFooter({ text: `Requested by: ${interaction.member.user.tag}` })
                ]
            })
        }


        let badge
        if (data.badges.length === 0) badge = `You not have any badges click [here](https://discord.gg/astrozmusic) to buy premium of **${client.user.tag}** to get badges!`
        else badge = data.badges.join('\n')
            .replace('owner', "<:owner:903695948931280936> Owner")
            .replace('dev', "<:dev:983678461170290688> Developer")
            .replace("staff", "<:staff:983678918068412446> Staff")
            .replace('mod', "<:mod:929956950526017558> Moderator")
            .replace("supporter", "<a:supporter:983679170628436001> Early Supporter")
            .replace("bug", "<:bughunter:981788204422266911> Bug Hunter")
            .replace('premium', "<:premium:908315637112250389> Premium User")
            .replace('friend', "<a:friend:983679942690754590> Friend")
        const embed = new MessageEmbed()
            .setColor(config.embedColor)
            .setAuthor({ name: 'Profile' })
            .setDescription(`Name: **${member.tag}**\n Id: **${member.id}**`)
            .addField("__Astroz Achievements__", badge)
            .setFooter({ text: `Requested by: ${interaction.member.user.tag}` })
        interaction.reply({ embeds: [embed] })
    },
};
```

## Badge Add & Remove Command
```js
const { MessageEmbed, CommandInteraction, Client, MessageAttachment } = require('discord.js');
const config = require('path of config');
const owner = "Owner ID"
const profile = require('path of schema')

module.exports = {
    name: 'badge',
    description: 'give badge to your frinds.',
    options: [
        {
            name: "add",
            description: 'add badge in profile!',
            type: 1,
            options: [
                {
                    name: 'user',
                    description: 'provide user',
                    type: 'USER',
                    required: true
                },
                {
                    name: 'badge',
                    description: 'Provide badge you want to add in profile',
                    type: 3,
                    required: true,
                    choices: [
                        {
                            name: "Owner",
                            value: "owner"
                        },
                        {
                            name: "Developer",
                            value: "dev"
                        },
                        {
                            name: "Staff",
                            value: "staff"
                        },
                        {
                            name: "Moderator",
                            value: "mod"
                        },
                        {
                            name: "Early Supporter",
                            value: "supporter"
                        },
                        {
                            name: "Premium User",
                            value: "premium"
                        },
                        {
                            name: "Friend",
                            value: "friend"
                        }
                    ]
                }
            ]
        },
        {
            name: "remove",
            description: 'remove badge in profile!',
            type: 1,
            options: [
                {
                    name: 'user',
                    description: 'provide user',
                    type: 'USER',
                    required: true
                },
                {
                    name: 'badge',
                    description: 'Provide badge you want to remove from profile',
                    type: 3,
                    required: true,
                    choices: [
                        {
                            name: "Owner",
                            value: "owner"
                        },
                        {
                            name: "Developer",
                            value: "dev"
                        },
                        {
                            name: "Staff",
                            value: "staff"
                        },
                        {
                            name: "Moderator",
                            value: "mod"
                        },
                        {
                            name: "Early Supporter",
                            value: "supporter"
                        },
                        {
                            name: "Premium User",
                            value: "premium"
                        },
                        {
                            name: "Friend",
                            value: "friend"
                        }
                    ]
                }
            ]
        }
    ],

    /**
     *
     * @param {Client} client
     * @param {CommandInteraction} interaction
     */

    run: async (client, interaction) => {
        if (interaction.member.id !== owner) return interaction.reply({
            embeds: [
                new MessageEmbed()
                    .setColor(config.embedColor)
                    .setDescription(`<:xd:983727243077492846> Only **Oᴍ !!!#9649** can execute this command!`)
            ]
        })
        const subcommand = interaction.options.getSubcommand()
        const badge = interaction.options.getString('badge')
        const member = interaction.options.getUser('user')
     
        const data = await profile.findOne({ id: member.id })
        if (!data) await profile.create({ id: member.id })

        if (subcommand === "add") {
            if (badge === "owner") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("owner")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Owner** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('owner')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Owner** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "dev") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("dev")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Developer** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('dev')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Developer** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "staff") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("staff")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Staff** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('staff')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Staff** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "mod") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("mod")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Moderator** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('mod')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Moderator** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "friend") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("friend")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Friend** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('friend')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Friend** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "premium") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("premium")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Premium User** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('premium')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Premium User** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "bug") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("bug")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Bug Hunter** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('bug')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Bug Hunter** badge in ${member} profile!`)
                    ]
                })
            }
            if (badge === "supporter") {
                const data = await profile.findOne({ id: member.id })
                if (data.badges.includes("supporter")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Early Supporter** badge is already exist in ${member} profile!`)
                    ]
                })
                await data.badges.push('supporter')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Added **Early Supporter** badge in ${member} profile!`)
                    ]
                })
            }
        }

        if (subcommand === "remove") {
            if (badge === "owner") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("owner")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Owner** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('owner')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Owner** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "dev") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("dev")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Developer** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('dev')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Developer** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "staff") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("staff")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Staff** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('staff')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Staff** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "mod") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("mod")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Moderator** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('mod')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Owner** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "friend") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("friend")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Friend** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('friend')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Friend** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "premium") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("premium")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Premium User** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('premium')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Premium User** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "bug") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("bug")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Bug Hunter** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('bug')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Bug Hunter** badge from ${member} profile!`)
                    ]
                })
            }
            if (badge === "supporter") {
                const data = await profile.findOne({ id: member.id })
                if (!data.badges.includes("supporter")) return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.error} **Early Supporter** badge is not exist in ${member} profile!`)
                    ]
                })
                await data.badges.remove('supporter')
                await data.save()
                return interaction.reply({
                    embeds: [
                        new MessageEmbed()
                            .setColor(config.embedColor)
                            .setDescription(`${config.done} Removed **Early Supporter** badge from ${member} profile!`)
                    ]
                })
            }
        }

    }
}
```

Congrats. You now have a fully working badge system integrated.
<br> 
Create pull request if you fixed bug or improved!
<br> 
Made with ❤️ by Oᴍ !!!#9649



