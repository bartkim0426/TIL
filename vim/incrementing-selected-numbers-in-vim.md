# Incrementing selected numbers in vim

Vim command for incrementing numbers sequentially.

```
my_array[0] = 0;
my_array[0] = 0;
my_array[0] = 0;
my_array[0] = 0;
my_array[0] = 0;
my_array[0] = 0;
my_array[0] = 0;
```

If you want to increment numbers inside my_array

### 1. with cursor on the first `0` in the first line

![image](https://i.imgur.com/1RVORXw.png)

### 2. blockwise select with `Ctrl-V`

- enter blockwise-visual mode
- and select all wanted number

Vim keys are will be...

```
<Ctrl-V>
6j  # or simply jjjjjj
```

> `v` for visual mode, `V` for line-visual mode, `Ctrl-V` for blockwise visual mode. For more information, read `:help ctrl-v`

Then you'll got selected numbers

![image](https://imgur.com/bmL8D6S)

### 3. increment with `g Ctrl-A`

- `<Ctrl-A>` is add count to the number or alphabetic character.
- `g <Ctrl-A` each one will be incremented if several lines are selected

> You can see help text in `:help ctrl-a`

Vim key strokes will be

```
g
<Ctrl-a>
```

Then, boom!


### All in one

So, all keystrokes will be (NO enter, newline is just for readability

It suppose to start from first line, first character (`m`)

```
f0  # find 0
<Ctrl-V>
6j
g
<Ctrl-a>
```

Here's whole gif

![image](https://imgur.com/QGfMUCp)
