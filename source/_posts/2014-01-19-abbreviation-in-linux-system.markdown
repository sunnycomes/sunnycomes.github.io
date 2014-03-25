---
layout: post
title: "Abbreviation in Linux System"
date: 2014-01-19 09:42:19 +0800
comments: true
tags: Linux
published: true

---
Unix is 35. Some people consider that the official start of middle age, although for a computer technology it's more like existing since the Middle Ages. Over that time, Unix and its child Linux have collected a lot of history and some quite archaic language. In this tip, we'll have a look at a few of the more ancient bits of crusty stuff and identify their purposes, if not their actual origins.

###RC
Perhaps the most common abbreviation in Linux is "rc," short for "runcomm" -- short in term for "run command." Today, "rc" is a suffix added to any script-like file that is called during the startup stage of a program, or of Linux in general. Thus /etc/rc is the master script for Linux startup, and .bashrc is the script that runs when the bash shell starts. The "." prefix on .bashrc is a naming standard designed only to hide user-specific administrative files from user files; the "ls" command doesn't list such files by default, and "rm" doesn't delete them by default. Many programs demand that startup files or profile files begin with a period or have an "rc" suffix, but there's nothing magical about either from the file system's perspective.

<!-- more -->

###ETC
The "etc" in "/etc/bin" really does stand for "etcetera." In early Unix systems, the most important directory was the "bin" directory (short for "binaries" -- compiled programs), and "etc" was for trivial stuff like startup, shutdown and admin. The list of things you need for running Linux is: a program binary, etcetera, etcetera -- in other words, a sole vital item, plus some less important bits and pieces. Today, "etc" holds system-wide configuration files that you'd almost never do without -- hardly unimportant.

###Bin
Most large subsystems that run on Linux today, like GNOME or Oracle, use their own "bin" directories (or /usr/bin or /usr/local/bin) as a standard place for compiled programs. It's common now to see scripts in those directories too, since "bin" directories are frequently added the PATH of a user, making their run-able programs available. So run-able scripts are often stuck in bin as well.

###TTY
Perhaps the most confusing jargon in Linux relates to terminals. TTY is an old abbreviation for a TeleTYpe. Teletypes, or teletypewriters, were originally printer-keyboard combinations that read and sent information over a serial line, not too different from an ancient telegraph machine. Later on, when computers only ran in batch mode (when card readers were the only way to get your program loaded), a teletype was the only useful "real time" input/output device available. Eventually teletypes were replaced with keyboard-and-screen terminals, but the operating system still needed a program to watch the serial port where the terminal or TTY was plugged in. That's what a getty "GEt TTY" process is: a program watching a physical TTY/terminal port. A Pseudo-TTY (a fake TTY, a "PTY") is the terminal equivalent of a virtual network computing (VNC) server. When you run an xterm or GNOME Terminal program, the PTY acts as a TTY for the virtual or pseudo terminal that the xterm represents. "Pseudo," meaning "duplicating in a fake way," is really a more descriptive term than "virtual" or "emulated." It's a shame that it has fallen out of fashion in computing.


/sbin = Superuser BINaries

/usr = Unix Shared Resources

Tk = ToolKit

VT = Video Terminal

YaST = Yet Another Setup Tool

apt = Advanced Packaging Tool

awk = "Aho Weiberger and Kernighan", Acronyms of Three Author's Names.

bash = Bourne Again SHell

biff = Dog's Name of the Author

cal = CALendar

cat = CATenate

cd = Change Directory

cmp = compare

cobra = Common Object Request Broker Architecture

comm = common

cpio = CoPy In and Out

cpp = C Pre Processor

cron = Chronos

cups = Common Unix Printing System

daemon = Disk And Execution MONitor

dc = Desk Calculator

dd = Disk Dump

df = Disk Free

dmesg = diagnostic message

du = Disk Usage

ed = editor

egrep = Extended GREP

elf = Extensible Linking Format

elm = ELectronic Mail

emacs = Editor MACroS

eval = EVALuate

ex = EXtended

exec = EXECute

fd = file descriptors

fg = ForeGround

fgrep = Fixed GREP

fmt = format

fsck = File System ChecK

fstab = FileSystem TABle

fvwm = F*** Virtual Window Manager

gawk = GNU AWK

gpg = GNU Privacy Guard

groff = GNU troff

hal = Hardware Abstraction Layer

joe = Joe's Own Editor

ksh = Korn SHell

lame = Lame Ain't an MP3 Encoder

lex = LEXical analyser

lisp = LISt Processing = Lots of Irritating Superfluous Parentheses

lpr = Line PRint

lsof = LiSt Open Files

m4 = Macro processor Version 4

mawk = Mike Brennan's AWK

mc = Midnight Commander

mknod = MaKe NODe

motd = Message of The Day

mtab = Mount TABle

nawk = New AWK

nl = Number of Lines

nm = names

nohup = No HangUP

nroff = New ROFF

od = Octal Dump

pg = pager

pico = PIne's message COmposition editor

pine = "Program for Internet News & Email" = "Pine is not Elm"

ping = Packet InterNet Grouper

pirntcap = PRINTer CAPability

popd = POP Directory

pr = pre

ps = Processes Status

pty = pseudo tty

pushd = PUSH Directory

rc = runcom = run commands, also shell of plan9

rev = REVerse

rn = Read News

roff = RunOFF

rpm = RPM Package Manager = RedHat Package Manager

rsh, rlogin, rvim, of which r stands for Remote

rxvt = ouR XVT

sed = Stream EDitor

seq = SEQuence

shar = SHell ARchive

stty = Set TTY

su = Switch User

tar = Tape ARchive

tcsh = TENEX C shell

tee = T

telnet = TEminaL over Network

termcap = terminal capability

terminfo = terminal information

tr = traslate

troff = Typesetter new ROFF

tsort = Topological SORT

tty = TeleTypewriter

twm = Tom's Window Manager

tz = TimeZone

udev = Userspace DEV

ulimit = User's LIMIT

umask = User's MASK

uniq = UNIQue

vi = VIsual = Very Inconvenient

vim = Vi IMproved

wall = write all

wc = Word Count

wine = WINE Is Not an Emulator

xargs = eXtended ARGuments

xdm = X Display Manager

xlfd = X Logical Font Description

xmms = X Multimedia System

xrdb = X Resources DataBase

xwd = X Window Dump

yacc = yet another compiler compiler

Fish = the Friendly Interactive SHell


##References:
1. ***[Excavating ancient abbreviations in Linux](http://searchenterpriselinux.techtarget.com/tip/Excavating-ancient-abbreviations-in-Linux)***