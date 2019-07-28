# Cartographer

## 1. Check directory

We can find `panel.php` which redirect to home.
It makes us realized that there are some authrization process in the website.

## 2. Do SQL injuction

Put a payload below into the user and pass form.

```
'or'1'='1
```

## 3. Search the parameter

We can find how this website send tha parameter.

```
http://docker.hackthebox.eu:34484/panel.php?info=home
```

### 4. Put some random value to the parameter.

I used wFuzz, but it did not work, so just put `flag` as a data.
Luckily, It returns the falg.

