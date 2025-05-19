
## 🔍 What Do Double Quotes Do?

Double quotes **preserve the value of most characters**, but **allow variable expansion and command substitution** inside.

```bash
echo "$USER"
```

This prints the value of the `USER` variable. If you **omit the quotes**, it may still work... *unless there's whitespace or special characters involved*—and then things get messy.

---
## 🎯 When to Use Double Quotes

### ✅ 1. **When Expanding Variables**

Always quote variables **unless you're deliberately splitting them**.

```bash
echo "$HOME"
echo "$1"
echo "$myvar"
```

Why? Because if a variable contains spaces, unquoted expansion will break your script.

Example:

```bash
FILENAME="My File.txt"
rm $FILENAME   # BAD: will try to delete "My" and "File.txt"
rm "$FILENAME" # GOOD: removes the full file
```

---

### ✅ 2. **When Using Command Substitution**

If you use `$(...)` or backticks, quote the result to prevent word splitting.

```bash
DATE="$(date +%F)"
echo "$DATE"
```

---

### ✅ 3. **When Using Strings with Spaces or Special Characters**

```bash
MESSAGE="Hello, world!"
echo "$MESSAGE"
```

If unquoted, Bash would treat the comma and space as separators. Quotes keep the whole string intact.

---

### ✅ 4. **When Passing Arguments to Commands**

Especially commands that expect a single path or string.

```bash
grep "$pattern" "$filename"
```

If `filename="my document.txt"`, unquoted use breaks.

---

### ✅ 5. **When Using Strings with Glob Characters (`*`, `?`)**

Unquoted variables can be interpreted as wildcards.

```bash
filename="*"
ls "$filename"  # looks for a file literally named "*"
ls $filename    # expands to match all files in current dir
```

---

## ❌ When *Not* to Use Double Quotes

### 1. **If You *Want* Word Splitting**

Say you're reading multiple words into an array:

```bash
words="apple banana cherry"
for word in $words; do
  echo "$word"
done
```

Quoting `$words` would prevent the loop from splitting it.

### 2. **Using Arrays and You Want Each Element Split**

```bash
arr=(one "two three" four)
printf '%s\n' "${arr[@]}"   # Correct
printf '%s\n' "${arr[*]}"   # Treats it as one big string
```

---

## 🧠 Summary: Golden Rule

> **When in doubt, quote it out.**

If you're expanding a variable or command and you want the result treated as **one single value**, use double quotes:

```bash
"$var" vs $var → safer, predictable, less painful debugging
```

