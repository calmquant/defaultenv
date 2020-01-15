# defaultenv

Environment and config file reader for python3.
**Warning:** slightly magical inside. This module magically reads and uses environment values both from '.env' file and environment itself.

*Since version 0.0.6 `.env` file will be reread on the fly on next `env` call, so now your environment is always up to date.*

```python
$ echo "MY_VAL='test'" > .env
$ python
>>> from defaultenv import env
>>> env('PATH')
'/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin'
>>> env('TEST')
>>> env('MY_VAL')
'test'
>>> import os; os.environ['MY_VAL']
'test'

```

`env` method may be used to check the value of variable.
If variable is not defined `env` method will return `None`.
If both environment variable and corresponding .env record exist then  .env have a priority.
And yes, you can use `os.environ` instead of  `env()`, all records from .env will be exported immediately.

For additional convenience you can use `env()` with `default` argument, for example:

```python
>>> from defaultenv import env
>>> env('TEST', 'no test')
'no test'
>>> env('UID')
'1000'
>>> env('UID', int)
1000
>>> env('HOME', pathlib.Path)
PosixPath("/home/bobuk")
>>> env('PATH', lambda x: [pathlib.Path(_) for _ in x.split(':')])
[PosixPath('/usr/local/sbin'), PosixPath('/usr/local/bin'), PosixPath('/usr/sbin'), PosixPath('/usr/bin'),
 PosixPath('/sbin'), PosixPath('/bin')]
```

If `default` argument for `env()` is not empty, and key that you looking for does not exist `env()` will return you value of the default.
But if `default` is callable (like object, lambda or function) then instead the value of key from environment will be passed to this callable.
My favorite is to send just `int` because it's the easiest way to convert your default to integer.

Since version 0.0.2 for more convenience two classes (ENV and ENVC) were added. You can use your environment variable name without method calling.

```python
$ python
>>> from defaultenv import ENV
>>> ENV.PATH
'/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin'
```

ENV usage removing unnecessary typing of parenthesis and quotes. In this example I've saved 4 keystrokes for you. 

But it's not very convenient to type UPPERCASE NAME every time. Let's imagine that my pinky finger is killing me.

```python
$ python
>>> from defaultenv import ENVC as env
>>> env.shell
'/usr/local/bin/zsh'
>>> env.home
'/home/bobuk'
```

As you can see `ENVC` converts your variable name to uppercase.
For both ENVC and ENV there's a method `defaults` to add default values of callables (as for `env` above).

```python
>>> from defaultenv import ENVC as env
>>> env.defaults(test = 1, path = lambda x: x.split(':'), pid=int)
>>> env.test
1
>>> env.path
['/usr/local/sbin', '/usr/local/bin', '/usr/sbin', '/usr/bin', '/sbin', '/bin']
```

What is the difference between `os.environ.get('PATH', None)` and `env.path`? It's easy to calculate and the answer is 21 (which is half of 42).

Since version 0.0.9 you can use even more automated `ENVCD` which is `ENVC` but with predefined defaults.
Every path (or colon-separated paths list) will be defaulted to `PosixPath`, every digital value converted to `int`.

```python
>>> from defaultenv import ENVCD as env
>>> env.path
[PosixPath('/usr/local/bin'), PosixPath('/usr/local/bin'), PosixPath('/usr/bin'), PosixPath('/bin'), PosixPath('/usr/sbin')]
```
