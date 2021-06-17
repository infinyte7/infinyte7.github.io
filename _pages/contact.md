---
title: "Contact"
permalink: "/contact.html"
---

<form name="gform" id="gform" enctype="text/plain" action="https://docs.google.com/forms/d/e/1FAIpQLSfZz1A3jUvtXTieUN8ipIyqLmJOYPRBGEfulVqfbaEy0i32Og/formResponse?" target="hidden_iframe" onsubmit="submitted=true;">
  

<div class="form-group row">
    <div class="col">
        <input type="text" class="form-control" name="entry.421446644" id="entry.421446644"
            placeholder="Name*">
    </div>
    <div class="col">
        <input type="email" class="form-control" name="entry.830990857" id="entry.830990857"
            aria-describedby="emailHelp" placeholder="Email*">
    </div>
</div>
<textarea class="form-control mb-3" name="entry.1168172121" id="entry.1168172121"
        placeholder="Message*" rows="8"></textarea>

  <input type="submit" value="Submit"  class="btn btn-success">
</form>

<script type="text/javascript">var submitted=false;</script>

<iframe name="hidden_iframe" id="hidden_iframe" style="display:none;" onload="if(submitted) {}"></iframe>


<script type="text/javascript">
$('#gform').on('submit', function(e) {
  $('#gform *').fadeOut(2000);
  $('#gform').prepend('<div class="alert alert-success" role="alert">Your submission has been processed...</div>');
  });
</script>