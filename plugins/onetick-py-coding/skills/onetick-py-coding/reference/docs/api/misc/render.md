# otp.utils.render_otq

### ``class render_otq(path, image_path=None, output_format=None, load_external_otqs=True, view=False, line_limit=(10, 60), parse_eval_from_params=False, render_debug_info=False, debug=False, graphviz_compat_mode=False, font_family=None, font_size=None)``

Bases:

Render queries from .otq files.

* **Parameters:**
  * **path** (*str* *,* *list* **[*str* *]*) – Path to .otq file or list of paths to multiple .otq files.
    Needed to render query could be specified with the next format: path_to_otq::query_name
  * **image_path** (*str* *,* *None*) – Path for generated image. If omitted, image will be saved in a temp dir
  * **output_format** (*str* *,* *None*) – Graphviz rendering format. Default: svg.
    If image_path contains one of next extensions, output_format will be set automatically: png, svg, dot.
  * **load_external_otqs** (*bool*) – If set to True (default) dependencies from external .otq files (not listed in `path` param)
    will be loaded automatically.
  * **view** (*bool*) – Defines should generated image be shown after render.
  * **line_limit** (*tuple* **[*int* *,* *int* *]* *,* *None*) – Limit for maximum number of lines and length of some EP parameters strings.
    First param is limit of lines, second - limit of characters in each line.
    If set to None limit disabled.
    If one of tuple values set to zero the corresponding limit disabled.
  * **parse_eval_from_params** (*bool*) – Enable parsing and printing eval sub-queries from EP parameters.
  * **render_debug_info** (*bool*) – Render additional debug information.
  * **debug** (*bool*) – Allow to print stdout or stderr from Graphviz render.
  * **graphviz_compat_mode** (*bool*) – Change internal parameters of result graph for better compatibility with old Graphviz versions.
    Could produce larger and less readable graphs.
  * **font_family** (*str* *,* *optional*) – 

    Font name

    Default: **Monospace**
  * **font_size** (*int* *,* *float* *,* *optional*) – Font size
* **Return type:**
  Path to rendered image

##### Examples

Render single file:

```
>>> otp.utils.render_otq("./test.otq")  
```

!`image`

Render multiple files:

```
>>> otp.utils.render_otq(["./first.otq", "./second.otq"])  
```

!`image`

Render specific queries from multiple files:

```
>>> otp.utils.render_otq(["./first.otq", "./second.otq::some_query"])  
```

Change font type to **Times New Roman** and text size to **10**:

```
>>> otp.utils.render_otq("./test.otq", font_family="Times-Roman", font_size=10)  
```
