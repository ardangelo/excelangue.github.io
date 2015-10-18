---
layout: default
title: SmileBASIC Projects
---
{% include JB/setup %}

[SmileBASIC](http://smilebasic.com/en/) (also known in Japan as Petit Computer 3) is an application for Nintendo 3DS that allows users to write and execute programs written in SmileBASIC, a dialect of BASIC. 

There are three ways to share programs:
<ol>
	<li>Over the Internet using the official SmileBASIC servers</li>
	<li>Copy source to PC using Rei's [PetitModem](http://rei.to/petitmodem_en.html) software ([software mirror]({{ site.url}}/assets/files/PetitModemPC1.2.7.zip), SmileBASIC key `NKX3KE13`)</li>
	<li>Type in by hand :-)</li>
</ol>

## Projects

### SmileBASIC LISP

A LISP interpreter for SmileBASIC. I hope to eventually build a LISP development environment on top of this. Future features: Garbage collection and an interface with native SmileBASIC commands from within LISP.

Version History
<ul>
	<li>**0.1** 2015-10-18: Abstracted parsing and input, added LOAD primitive for file input</li>
	<li>**0.0** 2015-10-16: Successfully ported D. Menezes' [QBASIC LISP](https://www.ma.utexas.edu/users/dmenezes/lisp.html)</li>
</ul>