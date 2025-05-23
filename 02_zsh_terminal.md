<br>
<br>
<br>

```md

কাজঃ
- In archlinux, have `bash` for default shell.
- `Bash` is so simple but we can't Customization.

<br>

**To customization, we use zsh shell with Antigen**
- Auto suggestion command any many more.

```

<br>
<br>
<br>


# 1. To install Zsh and Antigen on Arch Linux, follow these steps:

### Step 1: Install Zsh
1. **Open a terminal.**
2. **Update your system’s package list:**
   ```bash
   sudo pacman -Syu
   ```
3. **Install Zsh:**
   ```bash
   sudo pacman -S zsh
   ```

### Step 2: Change Default Shell to Zsh
1. **Change your default shell to Zsh:**
   ```bash
   chsh -s $(which zsh)
   ```
   - You will be prompted to enter your password.

2. **Restart your terminal or log out and back in to apply the changes.**

### Step 3: Install Git
If you haven't installed Git yet, you need it to clone Antigen:
```bash
sudo pacman -S git
```

### Step 4: Install Antigen
1. **Clone the Antigen repository:**
   ```bash
   git clone https://github.com/zsh-users/antigen.git ~/.antigen
   ```

### Step 5: Configure Zsh with Antigen
1. **Open or create your `.zshrc` file in your home directory:**
- Use kate instead of bash
   ```bash
   nano ~/.zshrc
   ```
2. **Add the following lines to your `.zshrc` file to load Antigen and configure your plugins:**
   ```bash
   # Load Antigen
   source $HOME/.antigen/antigen.zsh

   # Load the oh-my-zsh's library.
   antigen use oh-my-zsh

   # Load the theme (example: robbyrussell)
   antigen theme robbyrussell

   # Load plugins
   antigen bundle git
   antigen bundle zsh-users/zsh-syntax-highlighting
   antigen bundle zsh-users/zsh-autosuggestions

   # Tell Antigen that you’re done
   antigen apply
   ```
   - You can customize the plugins and theme according to your preference.

3. **Save and close the file.** 
   - If you’re using `nano`, press `Ctrl + X`, then `Y`, and then `Enter`.

### Step 6: Apply Changes
1. **Reload your `.zshrc` file:**
   ```bash
   source ~/.zshrc
   ```

# My Current  `kate .zshrc`
```bash

#------------------zsh+antigen----------
source ~/antigen.zsh

# Load the oh-my-zsh's library.
antigen use oh-my-zsh

# Bundles from the default repo (robbyrussell's oh-my-zsh).
antigen bundle git
# antigen bundle heroku
antigen bundle pip
# antigen bundle lein
antigen bundle command-not-found

# Syntax highlighting bundle.
antigen bundle zsh-users/zsh-syntax-highlighting

# Load the theme.
antigen theme refined

antigen bundle MichaelAquilina/zsh-auto-notify
antigen bundle zsh-users/zsh-autosuggestions

# Tell Antigen that you're done.
antigen apply

#-----------------------Android_sdk--------------
export PATH=$PATH:/home/yasin/Android/Sdk/tools
export PATH=$PATH:/home/yasin/Android/Sdk/tools/bin
export PATH=$PATH:/home/yasin/Android/Sdk/platform-tools


#------------------ Google CHROME_EXECUTABLE -------------
export CHROME_EXECUTABLE=/opt/google/chrome/google-chrome

#------------------ Auto matecally open neofetch ----------
neofetch
