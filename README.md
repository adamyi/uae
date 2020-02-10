# uae (Unhackable App Engine)

Like Amazon Lambda. Deploy your website without worrying about VM/containers.

To use this infra, you only need to submit short pieces of code for python functions, see the following example.

The environment is sandboxed.

**Feel free to write other services using this infra**

## How-To

Example deploy:
```
---
urls:
  "/": |-
    uae_rsp = uaeutils.make_response("Hello World")
  "/ping": |-
    uae_rsp = uaeutils.make_response("Pong")
  "/add": |-
    num_a = int(request['args'].get("a"))
    num_b = int(request['args'].get("b"))
    uae_rsp = uaeutils.make_response("Answer is " + str(num_a + num_b))
  "/error": |-
    uae_rsp = uaeutils.errorpage("Oops our server has fallen asleep")
  "/redirect": |-
    uae_rsp = uaeutils.redirect("https://www.adamyi.com/")
  "/json": |-
    uae_rsp = uaeutils.make_response(uaeutils.json_encode({"json": "is_easy"}))
default_handler: |-
  uae_rsp = uaeutils.errorpage("The requested URL %s was not found on this server." % request['path'], code=404)
```

## Vulnerability

Could deploy functions to any domains due to wrong caching rules.

**Please test if there are any other unintended vulns (user authentication is yet to add, main focus: bypass sandbox, DoS)**

**Normal User**
Domain: example
Path: helloworld

Cache Key: example.unhackable.app/helloworld

**Hacker**
Domain: example.unhackable.app/haha
Path: lmao

Cache Key: example.unhackable.app/haha.unhackable.app/lmao

**This would make XSS possible on all GAE apps**

## Payload

Visit https://manage.unhackable.app/edit?app=test1.unhackable.app:8056/haha

```
---
urls:
  "/test": |-
    uae_rsp = uaeutils.make_response("hacked")
default_handler: |-
  uae_rsp = uaeutils.errorpage("The requested URL %s was not found on this server." % request['path'], code=404)
```

https://test1.unhackable.app/haha.unhackable.app:8056/test becomes hacked

## author
adamyi
