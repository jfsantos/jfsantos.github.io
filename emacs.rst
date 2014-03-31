My Emacs configuration for MATLAB, LaTeX, and Python
####################################################

:date: 2013-07-28 12:00
:tags: development
:category: blog
:slug: emacs-config
:author: jfsantos

Emacs is a main part of my daily life. I write code and papers using
it, so you could say that anything that my research will leave
(hopefully) for the next generations was probably typed using it. Not
anybody loves Emacs, and I will not try to convince you to use
it. This post is just a brief overview of all the Emacs goodness I
found during the last years and I believe you should be using too, if
you use Emacs to write anything in MATLAB, LaTeX and Python.

First things first: `Prelude`_ is the base configuration I have been
using during the last years. Prelude is a sane base configuration that
includes a lot of good things and saves a **lot** of time. If you use
any UNIX-like operating system, installing it is a matter of running
this command:

.. _Prelude: http://batsov.com/prelude/

.. code-block:: bash
  
  curl -L http://git.io/epre | sh

as long as you already have Emacs 24 (if you are still using Emacs 23,
go on and update it ASAP!). Note that this installs it to the default
Emacs configuration folder (``$HOME/.emacs.d``).

I know that I said Prelude is a sane base configuration, but I don't
like two things it includes by default: ``guru-mode`` (which basically
disables keyboard arrows to force you to learn how to navigate using
default Emacs commands) and ``whitespace-mode`` (which renders
whitespace characters with visible glyphs). To disable these (and to
add anything to a Prelude-based Emacs configuration), edit (you may
have to create it first) ``$HOME/.emacs.d/personal/init.el`` and add
the following lines:

.. code-block:: lisp
  # -*- mode: elisp -*-
  # vi: set ft=elisp :
  
  ;; Disable guru-mode (I like using arrows :p)
  (setq prelude-guru nil)
  ;; Disable whitespace-mode
  (setq prelude-whitespace nil)

Another thing you should do is to enable the Prelude modules you want
to use (and disable the ones you are never going to need). To do it,
copy ``$HOME/.emacs.d/sample/prelude-modules.el`` to
``$HOME/.emacs.d/prelude-modules.el``, remove the comment symbols
(``;``) from the modules you want to use and comment the ones you
don't want.

MATLAB
------

While MATLAB has a decent GUI (and a nice graphical debugger), the
editor is not very good (it does code completion, some hinting, and
that's all). Also, in my experience, the GUI is not reliable under
Linux. `matlab-emacs`_ is an Emacs package that adds a major mode to
edit MATLAB code and also enables a MATLAB shell from inside Emacs. To
install it, you can run this `script`_ from MATLAB to download all the
files and save them to ``$HOME/.emacs.d/personal/matlab-emacs``. Then,
to make Emacs automatically open ``.m`` files using ``matlab-mode``
and configure ``matlab-shell``, add the following lines to
``$HOME/.emacs.d/personal/init.el``:

.. _matlab-emacs: http://matlab-emacs.sourceforge.net/
.. _script: http://matlab-emacs.cvs.sourceforge.net/viewvc/*checkout*/matlab-emacs/matlab-emacs/dl_emacs_support.m?revision=1.2&pathrev=MAIN

.. code-block:: lisp
  # -*- mode: elisp -*-
  # vi: set ft=elisp :
  
  ;; Setting up matlab-mode
  (add-to-list 'load-path "~/.emacs.d/personal/matlab-emacs")
  (load-library "matlab-load")
  (custom-set-variables
   '(matlab-shell-command-switches '("-nodesktop -nosplash")))
  (add-hook 'matlab-mode-hook 'auto-complete-mode)
  (setq auto-mode-alist
      (cons
       '("\\.m$" . matlab-mode)
       auto-mode-alist))

There is much more in ``matlab-emacs`` than just code completion and
the shell. You can use Emacs to debug MATLAB code, send selected
regions to execute on MATLAB, and much more. Take a look at the following articles to have a better idea of the other features:

http://blog.angjookanazawa.com/post/8815280589/productivity-matlab-emacs-integration-more

http://blogs.mathworks.com/community/2009/09/14/matlab-emacs-integration-is-back/

Python
------

Prelude includes a Python module, but if you look deeper you will see
it just enables ``subword-mode`` (to make word moving commands jump
inside CamelCase words) and disables ``electric-indent-mode`` (to
disable automatic indentation after a line break), which is not much
(and you may even disagree about this setup!). If you would like code
completion, refactoring, code hinting, code navigation, inline
documentation, and more, `Elpy`_ is a good option. Installing it
depends on two steps, as it has both a Emacs and a Python package. You
can simply follow these `instructions`_ and be happy with a boatload
of features! You will also need to install either `Rope`_ or `Jedi`_
(from the Python side), which Elpy uses for code completion. Rope
appears to have better refactoring features, so that's what I
use. Note that you have to install Elpy and Rope to a place on your
default ``PYTHONPATH``, which you can do by running ``pip
install --user elpy rope``. This will install the packages to
``$HOME/.local/lib/python2.7/site-packages``.

.. _Elpy: https://github.com/jorgenschaefer/elpy
.. _instructions: https://github.com/jorgenschaefer/elpy/wiki/Installation
.. _Rope: http://rope.sourceforge.net/
.. _Jedi: https://github.com/davidhalter/jedi

LaTeX
-----

`AUCTeX`_, an amazing package for writing LaTeX using Emacs, is
already included on Prelude (as long as you activate the LaTeX
module). It supports auto-completing LaTex expressions and has a
"magic compilation" command: ``C-c C-c``. It checks the status of your
folder and runs either latex/pdflatex, bibtex, or opens the output
file. 

.. _AUCTeX: https://www.gnu.org/software/auctex/

RefTeX is another extension but it ships with Emacs since Emacs
24.3. If your ``.tex`` file includes a reference to a ``.bib`` file,
you can use the command ``C-c [`` to add a citation to any of the
references listed on that file.

I also recommend using ``writegood-mode`` to detect use of weasel
words, duplicate words and passive voice. You can install it by simply
running the command ``M-x package-install writegood-mode`` directly
from Emacs (as long as you are using Prelude as described previously).

This is the configuration I use for fine-tuning AUCTeX (use pdflatex by default, use RefTeX, configure viewers, enable ``writegood-mode``):

.. code-block:: lisp
  # -*- mode: elisp -*-
  # vi: set ft=elisp :
  
  ;; LaTeX configuration
  (setq TeX-auto-save t)
  (setq TeX-parse-self t)
  (setq-default TeX-master nil)

  (add-hook 'LaTeX-mode-hook 'visual-line-mode)
  (add-hook 'LaTeX-mode-hook 'flyspell-mode)
  (add-hook 'LaTeX-mode-hook 'LaTeX-math-mode)
  (add-hook 'LaTeX-mode-hook 'TeX-source-correlate-mode)

  (add-hook 'LaTeX-mode-hook 'turn-on-reftex)
  (setq reftex-plug-into-AUCTeX t)
  (setq TeX-PDF-mode t)

  (setq TeX-output-view-style
      (quote
       (("^pdf$" "." "evince -f %o")
        ("^html?$" "." "iceweasel %o"))))

  ;; Setting up writegood-mode
  (require 'writegood-mode)
  (global-set-key "\C-cg" 'writegood-mode)


Extras
------

If you want to tinker and add your own features to Emacs, you will
probably want to learn some Emacs Lisp. This is a nice and brief
tutorial to get a grasp of it:
http://bzg.fr/learn-emacs-lisp-in-15-minutes.html (and this should be
useful even if you don't want to create your own extensions, but just
add some fancy things to your Emacs config!).
