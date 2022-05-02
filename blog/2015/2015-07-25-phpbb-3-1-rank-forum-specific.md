---
title: phpBB 3.1 rank forum specific
author: svennd

date: 2015-07-25T19:42:20+00:00
url: /phpbb-3-1-rank-forum-specific/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - code
tags:
  - code
  - php
  - phpBB

---
While allot functions are available in phpBB assigning a rank that only shows on certain forum's is not one of them.  So I want an expert group that only shows rank "expert" in forums that a user can choice (custom field). I don't really care for symphony or phpBB's code base, so this is just a quick and very, very dirty hack.

So I only care about viewtopic.php, my hack includes using custom fields, (pretty much abusing)

from line 2006+

<pre>if (!empty($cp_row['blockrow']))
{
  foreach ($cp_row['blockrow'] as $field_data)
  {
    $template-&gt;assign_block_vars('postrow.custom_fields', $field_data);

    if ($field_data['S_PROFILE_CONTACT'])
    {
      $template-&gt;assign_block_vars('postrow.contact', array(
        'ID'		=&gt; $field_data['PROFILE_FIELD_IDENT'],
        'NAME'		=&gt; $field_data['PROFILE_FIELD_NAME'],
        'U_CONTACT'	=&gt; $field_data['PROFILE_FIELD_CONTACT'],
      ));
    }</pre>

I added

<pre># hack 
# check if the value is equal to the current forum name
if ($field_data['PROFILE_FIELD_VALUE'] == $topic_data['forum_name'])
{
  $template-&gt;assign_block_vars('postrow.custom_expert', array('IS_EXPERT_HERE' =&gt; 1));
}
# end of hack</pre>

This will iterate all custom fields, and if one of them is the same as the forum name. (so the user decided to pick that name out the dropdown), it will give 1. In the template file you can just use this to check:

<pre>&lt;!-- BEGIN custom_expert --&gt;
&lt;!-- IF not postrow.custom_expert.IS_EXPERT_HERE --&gt;
no expert
&lt;!-- ELSE --&gt;
expert
&lt;!-- ENDIF --&gt;
&lt;!-- END custom_expert --&gt;</pre>

Quick, easy, dirty, not upgrade-able, but hell it works.