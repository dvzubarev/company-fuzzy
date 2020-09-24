[![Build Status](https://travis-ci.com/jcs090218/company-fuzzy.svg?branch=master)](https://travis-ci.com/jcs090218/company-fuzzy)
[![MELPA](https://melpa.org/packages/company-fuzzy-badge.svg)](https://melpa.org/#/company-fuzzy)
[![MELPA Stable](https://stable.melpa.org/packages/company-fuzzy-badge.svg)](https://stable.melpa.org/#/company-fuzzy)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

# company-fuzzy
> Fuzzy matching for `company-mode'.

<p align="center">
  <img src="./etc/demo.gif"/>
</p>

Pure `elisp` fuzzy completion for `company-mode`. This plugin search through
all the buffer local `company-backends` and fuzzy search all candidates.

## Features

* *Work across all backends* - Any backend that gives list of string should work.
* *Only uses native `elisp` code* - I personally don't prefer any external
program unless is necessary.
* *Combined all backends to one backend* - Opposite to [company-try-hard](https://github.com/Wilfred/company-try-hard),
hence all possible candidates will be shown in the auto-complete menu.

## Differences from other alternatives

* [company-ycmd](https://github.com/abingham/emacs-ycmd)
  * Uses [ycmd](https://github.com/Valloric/ycmd) as backend to provide functionalities.
  * Quite hard to config properly.
* [company-flx](https://github.com/PythonNut/company-flx)
  * Uses library [flx](https://github.com/lewang/flx).
  * Only works with `elisp-mode` currently.

## Usage

You can enable it globally by adding this line to your config
```el
(global-company-fuzzy-mode 1)
```
Or you can just enable it in any specific buffer/mode you want.
```el
(company-fuzzy-mode 1)
```

Make sure you call either of these functions after all
`company-backends` are set and config properly. Because
this plugin will replace all backends to this minor mode
specific backend (basically take all backends away, so
this mode could combine all sources and do the fuzzy work).

### Sorting/Scoring backend

There are multiple sorting algorithms for auto-completion. You can choose your
own backend by customize `company-fuzzy-sorting-backend` variable like this.

```el
(setq company-fuzzy-sorting-backend 'alphabetic)
```

Currently supports these values,

* *none* - Gives you the raw result.
* *alphabetic* - Sort in the alphabetic order. (VSCode)
* *flx* - Sort by [flx](https://github.com/lewang/flx) matching engine. (Sublime Text)

Or implements your sorting algorithm yourself? Assgin the function to
`company-fuzzy-sorting-function` variable like this.

```el
(setq company-fuzzy-sorting-function (lambda (candidates)
                                       (message "%s" candidates)
                                       candidates))  ; Don't forget to return the candidaites!
```

### Prefix ontop

If you wish the prefix matchs ontop of all other selection, customize
this variable to `t` like the line below.

```el
(setq company-fuzzy-prefix-ontop t)
```

*P.S.
If you set `company-fuzzy-sorting-backend` to `'flx` then
you probably don't need this to be on because the `flx` scoring engine
already take care of that!*

### For annotation

You can toggle `company-fuzzy-show-annotation` for showing annotation or not.

```el
(setq company-fuzzy-show-annotation t)
```

You can also customize annotation using `format` variable.

* `company-fuzzy-annotation-format` => ` <%s>`

## Details

Since [company](https://github.com/company-mode/company-mode)
granted most control to users, every company backend developer
has different method of implementing company backend. It is hard
to manage all backends to one by varies of rules.

If you encountered the backend that does not work with this package; try
to add the backend to `company-fuzzy-no-prefix-backends`. Then this
package will try to find the list of candidates when without any prefix.
`company-yasnippet` is one example that doesn't accept any prefix, hence
if backend that doesn't gives candidates by any prefix or no prefix then
this package can't get the list of candidates to do the fuzzy work.

```el
(add-to-list 'company-fuzzy--no-prefix-backends 'company-yasnippet)
```

Another special case is some backends take full input in order to get the
full candidates. `company-files` is one of the backend that is in this
special case. If you encountered a backend that use to work; yet it does
not work after this mode is enabled. Try adding the backend to
`company-fuzzy-full-input-backends` variable like the code snippet below.

```el
(add-to-list 'company-fuzzy--no-prefix-backends 'company-files)
```

## Recommended Settings

There are something that `company` design it weirdly, in order to make this
plugin work smoothly I would recommend these `company`'s variables to be set.

```el
(use-package company
  :init
  (setq company-require-match nil)            ; Don't require match, so you can still move your cursor as expected.
  (setq company-tooltip-align-annotations t)  ; Align annotation to the right side.
  (setq company-eclim-auto-save nil)          ; Stop eclim auto save.
  (setq company-dabbrev-downcase nil)         ; No downcase when completion.
  :config
  ;; Enable downcase only when completing the completion.
  (defun jcs--company-complete-selection--advice-around (fn)
    "Advice execute around `company-complete-selection' command."
    (let ((company-dabbrev-downcase t))
      (call-interactively fn)))
  (advice-add 'company-complete-selection :around #'jcs--company-complete-selection--advice-around))
```

*P.S.
For the full configuration you can check out my configuration
[here](https://github.com/jcs090218/jcs-emacs-init/blob/master/.emacs.jcs/jcs-plugin.el).*

## Contribution

If you would like to contribute to this project, you may either
clone and make pull requests to this repository. Or you can
clone the project and establish your own branch of this tool.
Any methods are welcome!
