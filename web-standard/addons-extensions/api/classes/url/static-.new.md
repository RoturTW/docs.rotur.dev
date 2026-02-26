# (static) .new()

Creates an instanced URL class.

## Parameters

### Scheme (str) (defaults to browser choice or null)

_**web**_://wow.bouncy.flf/subdir/subdir2/shocker.txt?test=wow\&crazy=noway

### Domain name (str) (defaults to browser choice or null)

web://wow._**bouncy**_.flf/subdir/subdir2/shocker.txt?test=wow\&crazy=noway

### Domain top (str) (defaults to browser choice or null)

web://wow.bouncy._**flf**_/subdir/subdir2/shocker.txt?test=wow\&crazy=noway

### Domain sub (str) (defaults to null)

web://_**wow**_.bouncy.flf/subdir/subdir2/shocker.txt?test=wow\&crazy=noway

### Path (str) (defaults to an empty string)

web://wow.bouncy.flf/_**subdir/subdir2**_/shocker.txt?test=wow\&crazy=noway

### Params (obj) (defaults to null)

web://wow.bouncy.flf/subdir/subdir2/shocker.txt?_**test=wow\&crazy=noway**_

### File Name (str) (defaults to index.osl, whatever other file type)

web://wow.bouncy.flf/subdir/subdir2/_**shocker.txt**_?test=wow\&crazy=noway
