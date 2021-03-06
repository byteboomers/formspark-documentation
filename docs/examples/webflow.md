---
title: Webflow
lang: en-US
---

# Webflow

1. Copy your form's action URL.
2. Paste the action URL into Webflow's `action` field in the form element’s settings in the Designer.
3. Copy-paste the code below into `Project Settings > Custom Code > Footer Code`.

![Webflow action](../.vuepress/public/webflow-action.jpeg)

```html
<!-- Project Settings > Custom Code > Footer Code -->

<script type="text/javascript">
  $('form[action^="https://submit-form.com"]').each(function (i, el) {
    var form = $(el);
    form.submit(function (e) {
      e.preventDefault();
      form = $(e.target);
      var action = form.attr("action");
      var data = form.serialize();
      $.ajax({
        url: action,
        method: "POST",
        data: data,
        dataType: "json",
        success: function () {
          var parent = $(form.parent());
          parent.children("form").css("display", "none");
          parent.children(".w-form-done").css("display", "block");
        },
        error: function () {
          var parent = $(form.parent());
          parent.find(".w-form-fail").css("display", "block");
        },
      });
    });
  });
</script>
```

:::tip
Check out this ["How to submit Webflow forms to your own backend"](https://technotrampoline.com/articles/how-to-submit-webflow-forms-to-your-own-backend/) article for more tips and tricks.
:::

## With Botpoison

```html
<!-- Project Settings > Custom Code > Footer Code -->

<script src="https://unpkg.com/@botpoison/browser"></script>

<script type="text/javascript">
  $('form[action^="https://submit-form.com"]').each(function (i, el) {
    var form = $(el);
    form.submit(function (e) {
      e.preventDefault();
      form = $(e.target);
      var action = form.attr("action");
      var botpoison = new Botpoison({
        publicKey: "your-public-key",
      });
      botpoison
        .challenge()
        .then(function (result) {
          var data = form.serialize();
          data += "&_botpoison=" + result.solution;
          $.ajax({
            url: action,
            method: "POST",
            data: data,
            dataType: "json",
            success: function () {
              var parent = $(form.parent());
              parent.children("form").css("display", "none");
              parent.children(".w-form-done").css("display", "block");
            },
            error: function () {
              var parent = $(form.parent());
              parent.find(".w-form-fail").css("display", "block");
            },
          });
        })
        .catch(function () {
          var parent = $(form.parent());
          parent.find(".w-form-fail").css("display", "block");
        });
    });
  });
</script>
```
