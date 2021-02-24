# TOBPrototype1

import discord
from discord.ext import commands
from discord.ext.commands import has_permissions
import typing
import random

bot = commands.Bot(command_prefix="t!", intents=discord.Intents.all())
bot.warnings = {} # guild+id : {member_id: [count, [(admin_id, reason)]]}

bot.remove_command("help")


list = ["fuck", "shit", "nigger", "nigga", "faggot", "tranny", "slut", "cunt", "ass", "asshole", "pussy","porn", "dick", "negro", "pedo", "n i g g a", "retard", "retarded"]

@bot.event
async def on_ready():
    print(f"Logged in!")



@bot.command()
async def ping(ctx):
    await ctx.send(f"T```he ping of the bot is {bot.latency}```")

@bot.command()
async def TOB(ctx):
    await ctx.send("What is TOB? Tob is a multipurpose moderation bot.")


@bot.command(name="aboutuserinfo")
async def about_user_info(ctx):
    await ctx.send(f"```The command t!userinfo gives you access to information about a user.```")

@bot.command(name="aboutserverinfo")
async def about_server_info(ctx):
    await ctx.send(f"```The command t!serverinfo gives you access to information about the server.```")

@bot.command()
@commands.has_permissions(manage_messages=True)
async def mute(ctx, member: discord.Member, *, reason=None):
    guild = ctx.guild
    mutedRole = discord.utils.get(guild.roles, name="Muted")

    if not mutedRole:
        mutedRole = await guild.create_role(name="Muted")

    for channel in guild.channels:
    	await channel.set_permissions(mutedRole, speak=False, send_messages=False, read_message_history=True, read_messages=False)

    await member.add_roles(mutedRole, reason=reason)
    await ctx.send(f"Muted {member.mention} for reason {reason}")
    await member.send (f"You have been muted from the server {guild.name} for {reason}")

@bot.command()
@commands.has_permissions(manage_messages=True)
async def unmute(ctx, member: discord.Member):
    mutedRole = discord.utils.get(ctx.guild.roles, name="Muted")

    await member.remove_roles(mutedRole)
    await  ctx.send(f"Unmute {member.mention}")
    await member.send(f"You have been unmuted from the server {guild.name}")

@bot.command(name="aboutdelete")
async def about_delete(ctx):
    await ctx.send(f"```The command t!delete erases a specific number of messages..```")

@bot.command(name="aboutping")
async def about_delete(ctx):
    await ctx.send(f"```The command t!ping gives you the bot's ping.```")


@bot.command(name='8ball',
             description=" ",
             brief=" ",
             aliases=['eight_ball', 'eightball', '8-ball'],
             pass_context=True)
async def eight_ball(context):
    possible_responses = [
        'Without a doubt',
        'I dont think so',
        'Probably not',
        'I guess so',
        'Yes',
        'No',
        'Maybe',
        'What do you think?',
        'All the signs point to yes',
        'How would I know?',
        'No??',

    ]
    await context.send(random.choice(possible_responses) + ", " + context.message.author.mention)

@bot.command(name='coinflip')
async def coinflip(context):
    possible_responses = [
        'Head',
        'Tails',
    ]
    await context.send(random.choice(possible_responses) + ", " + context.message.author.mention)



@bot.command(name="userinfo")
async def user_info(ctx, Target:typing.Optional[discord.Member]):
	"""Displays user info. If no member is given, it defaults to the command invoker."""
	if Target is None:
		Target = ctx.author

	embed = discord.Embed(title=f"User Information - {Target.name}")
	embed.add_field(name="Account name", value=Target.name, inline=False)
	embed.add_field(name="Discriminator", value=Target.discriminator, inline=False)
	embed.add_field(name="ID", value=Target.id, inline=False)
	embed.add_field(name="Is Bot", value=Target.bot, inline=False)
	embed.add_field(name="Top Role", value=Target.top_role, inline=False)
	embed.add_field(name="Roles", value=len(Target.roles), inline=False)
	embed.add_field(name="Created at", value=Target.created_at.strftime("%d/%m/%Y %H:%M:%S"), inline=False)
	embed.add_field(name="Joined at", value=Target.joined_at.strftime("%d/%m/%Y %H:%M:%S"), inline=False)
	embed.set_thumbnail(url=Target.avatar_url)

	await ctx.send(embed=embed)
	return


@bot.command(name="serverinfo")
async def guild_info(ctx):
	"""Displays server information."""

	embed = discord.Embed(title=f"Server information - {ctx.guild.name}")
	embed.add_field(name="Name", value=ctx.guild.name, inline=False)
	embed.add_field(name="ID", value=ctx.guild.id, inline=False)
	embed.add_field(name="Region", value=str(ctx.guild.region).title(), inline=False)
	embed.add_field(name="Shard ID", value=ctx.guild.shard_id, inline=False)
	embed.add_field(name="Created at", value=ctx.guild.created_at.strftime("%d/%m/%y %H:%M:%S"), inline=False)
	embed.add_field(name="Most recent member", value=[Member for Member in ctx.guild.members if Member.joined_at is max([Member.joined_at for Member in ctx.guild.members])][0].display_name, inline=False)
	embed.add_field(name="... joined", value=max([Member.joined_at for Member in ctx.guild.members]).strftime("%d/%m/%y %H:%M:%S"), inline=False)
	embed.set_thumbnail(url=ctx.guild.icon_url)

	await ctx.send(embed=embed)
	return


@bot.command()
@has_permissions(manage_messages=True)
async def delete(ctx, amount=15):
    amount += 1
    await ctx.channel.purge(limit=amount)

@bot.command()
@commands.has_permissions(kick_members=True)
async def kick(ctx, user: discord.Member, *, reason=None):
    await user.kick(reason=reason)
    await ctx.send(f"{user} Has been kicked!")

@bot.command()
@commands.has_permissions(ban_members=True)
async def ban(ctx, user: discord.Member, *, reason=None):
    await user.ban(reason=reason)
    await ctx.send(f"{user} Has been banned!")

@bot.event
async def on_message(message):
    if message.content.startswith("t!"):
        await bot.process_commands(message)
    else:
        for word in list:
            if message.author.bot == False:
                if discord.utils.get(message.author.roles, name="[-] 𝘼𝙙𝙢𝙞𝙣𝙞𝙨𝙩𝙧𝙖𝙩𝙤𝙧") == None:
                    if word in message.content.lower():
                        await message.channel.send(f"{message.author.mention}\nThis word is banned from our server!")
                        await message.delete()

                    if "mail" in message.content.lower():
                        await message.channel.send(f"{message.author.mention}\nThis message has been deleted to be in co-operation of privacy.")
                        await message.delete()

                    if "https://" in message.content.lower():
                        await message.channel.send(f"{message.author.mention}\nThis message has been deleted to be in co-operation of privacy.")
                        await message.delete()

                    if "www." in message.content.lower():
                        await message.channel.send(f"{message.author.mention}\nThis message has been deleted to be in co-operation of privacy.")
                        await message.delete()

                    if "www." in message.content.lower():
                        await message.channel.send(f"{message.author.mention}\nThis message has been deleted to be in co-operation of privacy.")
                        await message.delete()


@bot.command()
async def help(ctx):
    embed = discord.Embed(
        title = 'Help Command	',
        colour = discord.Colour.red()
    )

    embed.set_footer(text='TOB.2021')
    embed.set_image(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_thumbnail(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_author(name=' ',
    icon_url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.add_field(name='delete', value='Deletes a selected number of messages.', inline=False)
    embed.add_field(name='8ball', value='8ball generated a random response to your question.', inline=True)
    embed.add_field(name='ping', value='Gives you the bot ping.', inline=False)
    embed.add_field(name='kick', value='Kicks a selected member.', inline=False)
    embed.add_field(name='TOB', value='Gives information about TOB.', inline=True)
    embed.add_field(name='serverinfo', value='Gives information about the server.', inline=False)
    embed.add_field(name='userinfo', value='Gives information about a selected user.', inline=False)
    embed.add_field(name='ban', value='Bans the selected user from the server.', inline=True)
    embed.add_field(name='invite', value='Gives you the link to invite TOB to your server.', inline=False)
    embed.set_footer(text='Do t!help2 for next page.')


    await ctx.send(embed=embed)

@bot.command()
async def help2(ctx):
    embed = discord.Embed(
        title = 'Help Command	',
        colour = discord.Colour.red()
    )

    embed.set_footer(text='TOB.2021')
    embed.set_image(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_thumbnail(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_author(name=' ',
    icon_url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.add_field(name='unmute', value='Unmutes the selected user.', inline=False)
    embed.add_field(name='mute', value='Mutes the selected user.', inline=False)
    embed.add_field(name='coinflip', value='Helps you on decision making.', inline=False)
    embed.set_footer(text='Page 2.')

    await ctx.send(embed=embed)





@bot.command()
async def version(ctx):
    embed = discord.Embed(
        title = 'Version / Updates',
        colour = discord.Colour.red()
    )

    embed.set_footer(text='TOB.2021')
    embed.set_image(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_thumbnail(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_author(name=' ',
    icon_url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.add_field(name='0.1.3', value='Kick, Ban, Mute, 8ball, and Coin Flip commands have been added.', inline=False)

    await ctx.send(embed=embed)

@bot.command()
async def helpuserinfo(ctx):
    embed = discord.Embed(
        title = 'Userinfo',
        colour = discord.Colour.red()
    )

    embed.set_footer(text='TOB.2021')
    embed.set_image(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_thumbnail(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_author(name=' ',
    icon_url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.add_field(name='Gives you information on a selected member.', value='t!helpuserinfo', inline=False)

    await ctx.send(embed=embed)

@bot.command()
async def invite(ctx):
    embed = discord.Embed(
        title = 'TOB invite Link',
        colour = discord.Colour.red()
    )

    embed.set_footer(text='TOB.2021')
    embed.set_image(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_thumbnail(url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.set_author(name=' ',
    icon_url='https://media.discordapp.net/attachments/747863930772258928/810387459774611507/TOB.png%27')
    embed.add_field(name='This is the link to invite TOB to your own server.', value='https://discord.com/api/oauth2/authorize?client_id=806027310553104464&permissions=8&scope=bot', inline=False)

    await ctx.send(embed=embed)







bot.run(token)
