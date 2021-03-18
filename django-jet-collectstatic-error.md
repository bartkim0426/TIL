# django jet collectstatic error

When using django-jet, always it occured that collectstatic for existing s3 breaks some css.

I figured out that it's because admin css directory is not overwritten by collectstatic command.

So if this happens, remove existing admin static directory and execute collectstatic command again
