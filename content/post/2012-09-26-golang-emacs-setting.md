+++
title = "emacs下golang的配置"
date = "2012-09-26T14:12:00+08:00"
comments = true
categories = ["programming", "tools"]
tags = ["emacs", "go"]
description = ""
+++


[参考链接](https://github.com/astaxie/build-web-application-with-golang/blob/master/1.4.md#emacs)

1. 语法高亮
```sh
cp $GOROOT/misc/emacs/* ~/.emacs.d/
```

2. 安装`gocode`
```sh
go get -u github.com/nsf/gocode
```

3. 配置`gocode`

<!--more-->

```sh
$ cd $GOPATH/src/github.com/nsf/gocode/emacs
$ cp go-autocomplete.el ~/.emacs.d/
$ gocode set propose-builtins true
propose-builtins true
$ gocode set lib-path "/Volumes/home/chenza/gopath/pkg/darwin_amd64" # 换为你自己的路径
lib-path "/Volumes/home/chenza/gopath/pkg/darwin_amd64"
$ gocode set
propose-builtins true
lib-path "/Volumes/home/chenza/gopath/pkg/darwin_amd64"
```

4. 配置`autocompletion`

安装:

```sh
make install DIR=$HOME/.emacs.d/auto-complete
```

5. 配置`.emacs`
```lisp
;;auto-complete
(require 'auto-complete-config)
(add-to-list 'ac-dictionary-directories "~/.emacs.d/auto-complete/ac-dict")
(ac-config-default)
(local-set-key (kbd "M-/") 'semantic-complete-analyze-inline)
(local-set-key "." 'semantic-complete-self-insert)
(local-set-key ">" 'semantic-complete-self-insert)

;; golang mode
(require 'go-mode-load)
(require 'go-autocomplete)
;; speedbar
;; (speedbar 1)
(speedbar-add-supported-extension ".go")
(add-hook
 'go-mode-hook
 '(lambda ()
   ;; gocode
   (auto-complete-mode 1)
   (setq ac-sources '(ac-source-go))
    ;; Imenu & Speedbar
    (setq imenu-generic-expression
          '(("type" "^type *\\([^ \t\n\r\f]*\\)" 1)
            ("func" "^func *\\(.*\\) {" 1)))
    (imenu-add-to-menubar "Index")
    ;; Outline mode
    (make-local-variable 'outline-regexp)
    (setq outline-regexp "//\\.\\|//[^\r\n\f][^\r\n\f]\\|pack\\|func\\|impo\\|cons\\|var.\\|type\\|\t\t*....")
    (outline-minor-mode 1)
    (local-set-key "\M-a" 'outline-previous-visible-heading)
    (local-set-key "\M-e" 'outline-next-visible-heading)
    ;; Menu bar
    (require 'easymenu)
    (defconst go-hooked-menu
      '("Go tools"
        ["Go run buffer" go t]
        ["Go reformat buffer" go-fmt-buffer t]
        ["Go check buffer" go-fix-buffer t]))
    (easy-menu-define
      go-added-menu
      (current-local-map)
      "Go tools"
      go-hooked-menu)

    ;; Other
    (setq show-trailing-whitespace t)
    ))
;; helper function
(defun go ()
  "run current buffer"
  (interactive)
  (compile (concat "go run " (buffer-file-name))))

;; helper function
(defun go-fmt-buffer ()
  "run gofmt on current buffer"
  (interactive)
  (if buffer-read-only
    (progn
      (ding)
      (message "Buffer is read only"))
    (let ((p (line-number-at-pos))
    (filename (buffer-file-name))
    (old-max-mini-window-height max-mini-window-height))
      (show-all)
      (if (get-buffer "*Go Reformat Errors*")
    (progn
      (delete-windows-on "*Go Reformat Errors*")
      (kill-buffer "*Go Reformat Errors*")))
      (setq max-mini-window-height 1)
      (if (= 0 (shell-command-on-region (point-min) (point-max) "gofmt" "*Go Reformat Output*" nil "*Go Reformat Errors*" t))
    (progn
      (erase-buffer)
      (insert-buffer-substring "*Go Reformat Output*")
      (goto-char (point-min))
      (forward-line (1- p)))
  (with-current-buffer "*Go Reformat Errors*"
    (progn
      (goto-char (point-min))
      (while (re-search-forward "<standard input>" nil t)
        (replace-match filename))
      (goto-char (point-min))
      (compilation-mode))))
      (setq max-mini-window-height old-max-mini-window-height)
      (delete-windows-on "*Go Reformat Output*")
      (kill-buffer "*Go Reformat Output*"))))
;; helper function
(defun go-fix-buffer ()
  "run gofix on current buffer"
  (interactive)
  (show-all)
  (shell-command-on-region (point-min) (point-max) "go tool fix -diff"))
```

