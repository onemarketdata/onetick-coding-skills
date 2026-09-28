# otp.Source.render_otq

#### ``Source.render_otq(image_path=None, output_format=None, load_external_otqs=True, view=False, line_limit=(10, 30), parse_eval_from_params=False, render_debug_info=False, debug=False, graphviz_compat_mode=False, font_family=None, font_size=None, **kwargs)``

Render current ``Source`` graph.

* **Parameters:**
  * **image_path** (*str* *,* *None*) – Path for generated image. If omitted, image will be saved in a temp dir
  * **output_format** (*str* *,* *None*) – Graphviz rendering format. Default: png.
    If image_path contains one of next extensions, output_format will be set automatically:
    png, svg, dot.
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
  * **font_size** (*int* *,* *float* *,* *str* *,* *optional*) – Font size
  * **kwargs** – Additional arguments to be passed to ``onetick.py.Source.to_otq()`` method (except
    `file_name`, `file_suffix` and `query_name` parameters)
* **Return type:**
  Path to rendered image

##### Examples

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')  
>>> data1, data2 = data[(data['PRICE'] > 50)]                                    
>>> data = otp.merge([data1, data2])                                             
>>> data.render_otq('./path/to/image.png')                                       
```

!`image`

##### SEE ALSO
``render_otq``
