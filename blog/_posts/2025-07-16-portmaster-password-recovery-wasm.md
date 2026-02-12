---
layout: post
category: projects
---

After buying a Livinston/Lucent Portmaster 2 a while back and being locked out of the machine due to not knowing the password, I eventually stumbled upon the wonderful [PortMaster password recovery tool](http://home.eversberg.eu/public/portmaster.php) by [jolly](http://eversberg.eu/contact/) and used it to instantly gain access to my Portmaster.

From what I can gather, the Portmaster has no local reset procedure, even if you have physical access to the machine, but will give you a _challenge code_ in the event that you no longer have the password. Back in the '90s you could provide this code to customer support who would then return to you a _response code_ (essentially just an MD5 digest composed of the CHALLENGE code and some extra bytes) that could be used to get into the machine. At that point you could change your password and carry on with your day.

Now though, customer support is long-gone, and this recovery tool emulates the whole interaction _in software_. 

I don't know the whole story behind the tool, but it seems like the original C implementation was written by [pmsac@toxyn.org](mailto:pmsac@toxyn.org), implementing an MD5 message-digest algorithm from Ron Rivest, written by Colin Plumb, with contributions by Ian Jackson and Stephen Early. The PHP tool, to my best guess, calls the original C code to get a response. If it doesn't do this, that means there is a PHP implementation that hasn't been made publicly available. 

## Wasm to the Wescue

This didn't site well with me, and I thought there should be a way to use this tool without needing to build/run a C program and/or host a PHP application if the original ever disappears.

[Wasm (WebAssembly)](https://webassembly.org/) seemed like the logical way to use the existing C code without a massive rewrite, and have the resulting tool available as a highly-portable application that can be copied into _any_ webserver directory and _just work_ was really enticing.

The original `mz.c` file was modified into a `mz_web.c` file with changes for Wasm. Notably, we cannot call a `main` function via Wasm, so this had to be modified.

[Emscripten](https://emscripten.org/) was used to build `mz_web`. If you have Emscripten installed yourself, you can replicate the compilation of `mz_web.c` via:

```
emcc mz_web.c -o mz_web.js \
  -sEXPORTED_FUNCTIONS='["_get_response", "_malloc", "_free"]' \
  -sEXPORTED_RUNTIME_METHODS='["cwrap", "UTF8ToString"]' \
  -sMODULARIZE -sENVIRONMENT=web
```

This generates `mz_web.js` and `mz_web.wasm`.

Full code for this can be found in the [portmaster-password-recovery-wasm](https://github.com/dialup-world/portmaster-password-recovery-wasm) repository.

## Live Demo & Usage

A live demo is available at [https://dialup.world/portmaster-password/](https://dialup.world/portmaster-password/). It seems to work in FireFox, Chrome, and Safari.

Anyone is free to run and host this themselves, it's as easy as cloning the repository and dropping the directory on a web server.

Upon loading the page you can follow these instructions on your Portmaster:

1. At login prompt enter: `!root`
2. At password prompt enter: `override`
3. Copy the 16 character `<CHALLENGE_STRING>` and enter it into the webpage to receive a `<RESPONSE_STRING>`
4. At login prompt enter again: `!root`
5. At password prompt enter: `<RESPONSE_STRING>`
6. Be sure to change password set password `<pw>` and save it via `save all`

