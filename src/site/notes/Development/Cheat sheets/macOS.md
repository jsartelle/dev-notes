---
{"dg-publish":true,"dg-path":"Cheat sheets/macOS.md","permalink":"/cheat-sheets/mac-os/"}
---


# Enable Touch ID for sudo

```shell
sudo cp /etc/pam.d/sudo_local.template /etc/pam.d/sudo_local
```

```shell
sudo nano /etc/pam.d/sudo_local
```

- Uncomment the last line (`#auth sufficient pam_tid.so`)
- Allow Touch ID when using remote desktop (including DisplayLink):

```
defaults write com.apple.security.authorization ignoreArd -bool TRUE
```

# Set custom keyboard shortcuts for nested menu items

```
Format->Indentation->Increase
```

# Terminal commands


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/cheat-sheets/terminal/#mac-os-specific" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## macOS-specific

### caffeinate

- `caffeinate`: prevent your Mac from sleeping until the process exits
- `caffeinate -t N`: prevent sleep for N seconds

### chflags

#### Hide/unhide files & folders

See [[#stat]] to check if a file/folder is hidden

```shell
chflags hidden file.txt
chflags nohidden file.txt
```

### defaults

#### Remove dock autohide delay

```shell
defaults write com.apple.dock autohide-delay -float 0;killall Dock
```

#### Dim Dock icons of hidden apps

```shell
defaults write com.apple.Dock showhidden -boolean yes; killall Dock
```

#### Add a spacer to the Dock

- Applications (left) side:

```shell
defaults write com.apple.dock persistent-apps -array-add '{tile-type="spacer-tile";}'; killall Dock
```

- Documents (right) side:

```shell
defaults write com.apple.dock persistent-others -array-add '{tile-data={}; tile-type="spacer-tile";}'; killall Dock
```

### tccutil

#### Reset app permissions

- Get the app's Bundle ID:

```shell
osascript -e 'id of app "Name of App"'
```

- Reset permissions
    - you can replace All with a category name, like `Photos`

```shell
sudo tccutil reset All bundle_id
```

### Homebrew

- `brew leaves`: show packages that aren't dependencies of any other installed package
    - `-r`: only show leaves that were manually installed
    - `-p`: only show leaves that were installed as dependencies of another package
        - these can be safely uninstalled, since the package that depended on them is no longer installed
- `brew cleanup -s`: remove unused and outdated cache files

### Moom

- adjust spacing for Stage Manager

```shell
defaults read com.manytricks.Moom "Grid Spacing: Apply To Edges: Gaps" {0,0,0,75}
```


</div></div>

