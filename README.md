# Serbea: Similar to ERB, Except Awesomer

Serbea is the love child of Liquid, Nunjucks, and ERB.

Make of that what you will.

Coming soon…

----

## Features

* Real Ruby. Like, for real.
* Filters!!! Pipeline operators!!!
* Autoescaped variables by default within the pipeline (`{{ }}`) tags. Use the `safe`/`raw` or `escape`/`h` filters to control escaping on output.
* Render directive `{%@ %}` as shortcut for rendering either string-named partials (`render "tmpl"`) or object instances (`render MyComponent.new`).
* Supports every convention of ERB and builds upon it with new features (which is why it's "awesomer!").
* Builtin frontmatter support (even in Rails!) so you can access the variables written into the top YAML within your templates.
* The filters accessible within Serbea templates are either helpers (where the variable gets passed as the first argument) or instance methods of the variable itself, so you can build extremely expressive pipelines that take advantage of the code you already know and love. (For example, in Rails you could write `{{ "My Link" | link_to: route_path }}`).

## What It Looks Like

```njk
# example.serb

{% wow = capture do %}
  This is {{ "amazing" + "!" | upcase }}
{% end.each_char.reduce("") do |newstr, c|
    newstr += " #{c}"
   end.strip %}

{{ wow | prepend: "OMG! " }}
```

```njk
<p>
  {%
    helper :multiply_array do |input, multiply_by = 2|
      input.map do |i|
        i.to_i * multiply_by
      end
    end
  %}

  Multiply! {{ [1,3,6, "9"] | multiply_array: 10 }}
</p>
```

```njk
{%= form classname: "checkout" do |f| %}
  {{ f.input :first_name, required: true | errors: error_messages }}
{% end %}
```

```njk
{%= render "box" do %}
  This is **dope!**
  {%= render "card", title: "Nifty!" do %}
    So great.
  {% end %}
{% end %}

# Let's simplify that using the new render directive!

{%@ "box" do %}
  This is **dope!**
  {%@ "card", title: "Nifty!" do %}
    So great.
  {% end %}
{% end %}
```

```njk
# Works with ViewComponent!

{%= render(Theme::DropdownComponent.new(name: "banner", label: "Banners")) do |dropdown| %}
  {% RegistryTheme::BANNERS.each do |banner| %}
    {% dropdown.slot(:item, value: banner) do %}
      <img src="{{ banner | parameterize: separator: "_" | prepend: "/themes/" | append: ".jpg" }}">
      <strong>{{ banner }}</strong>
    {% end %}
  {% end %}
{% end %}

# Even better, use the new render directive!

{%@ Theme::DropdownComponent name: "banner", label: "Banners" do |dropdown| %}
  {% RegistryTheme::BANNERS.each do |banner| %}
    {% dropdown.slot(:item, value: banner) do %}
      <img src="{{ banner | parameterize: separator: "_" | prepend: "/themes/" | append: ".jpg" }}">
      <strong>{{ banner }}</strong>
    {% end %}
  {% end %}
{% end %}
```
