---
layout: default
title: SmileBASIC Projects
---
{% include JB/setup %}

[SmileBASIC](http://smilebasic.com/en/) (also known in Japan as Petit Computer 3) is an application for Nintendo 3DS that allows users to write and execute programs written in SmileBASIC, a dialect of BASIC. 

There are three ways to share programs:
<ol>
	<li>Upload to SmileBASIC servers, download using a SmileBASIC key</li>
	<li>Copy source to PC using Rei's <a href="http://rei.to/petitmodem_en.html">PetitModem</a> software (<a href="{{ site.url}}/assets/files/PetitModemPC1.2.7.zip">PC mirror</a>, SmileBASIC key <code>NKX3KE13</code>)</li>
	<li>Type in by hand :-)</li>
</ol>

## Projects

### SmileBASIC LISP

A LISP-1 interpreter for SmileBASIC. I hope to eventually build a LISP development environment on top of this. Future features: Improved garbage collection, tail-call optimization, and an interface with native SmileBASIC commands from within LISP.

Sample LISP code with some simple functions: <code>XKA84J4F</code>

Version History
<ul>
	<li><b>0.4b</b> 2015-11-19: Working tail-call optimization. Optimizes self calls in tail position but still piles onto the heap. A work in progress (Key <code>X338V3AP</code>)</li>
	<li><b>0.3</b> 2015-10-26: Added PRINT statement and working mark-sweep garbage collection (Key <code>13WEEEHV</code>)</li>
	<li><b>0.1</b> 2015-10-18: Rewrote parts using more SmileBASIC, added LOAD primitive for file input. (Key <code>BDEE32HV</code>)</li>
	<li><b>0.0</b> 2015-10-16: Successfully ported D. Menezes' <a href="https://www.ma.utexas.edu/users/dmenezes/lisp.html">QBASIC LISP</a></li>
	<li>2015-10-15: SmileBASIC is released on the North American 3DS eShop</li>
</ul>