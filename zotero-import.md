---
cssclass: zotero-import
obsidianUIMode: preview
alias: [{% if shortTitle %}"{{shortTitle | safe}}"{% else %}"{{title | safe}}"{% endif %}]
title: "{{title}}"
tags: zotero-citations
---
{%- macro colorValueToName(color) -%}
	{%- switch color -%}
		{%- case "#ffff7f" -%}
			Relevant / important
		{%- case "#ff7f7f" -%}
			Disagree
		{%- case "#ffbf7f" -%}
			Questions / confusion
		{%- case "#7fff7f" -%}
			Agree
		{%- case "#7fffff" -%}
			Relevant to current task
		{%- case "#ff7fff" -%}
			TODO / follow up
		{%- case "#bf7fbf" -%}
			Definitions / concepts
		{%- default -%}
			Interesting but not relevant
	{%- endswitch -%}
{%- endmacro -%}

{%- macro calloutHeader(type) -%}
	{%- switch type -%}
		{%- case "highlight" -%}
			Highlight
		{%- case "strike" -%}
			Strikethrough
		{%- case "underline" -%}
			Underline
		{%- case "image" -%}
			Image
		{%- default -%}
			Note
	{%- endswitch -%}
{%- endmacro %}

> [!info]
> - **Cite Key:** [[@{{citekey}}]]
{%- for attachment in attachments | filterby("path", "endswith", ".pdf") %}
> - **Link:** [{{attachment.title}}](file://{{attachment.path | replace(" ", "%20")}})
{%- endfor -%}
{%- if abstractNote %}
> - **Abstract:** {{abstractNote}}
{%- endif -%}
{%- if bibliography %}
> - **Bibliography:** {{bibliography}}
{%- endif %}

## Annotations
{% persist "annotations" %}
{% set annots = annotations | filterby("date", "dateafter", lastImportDate) -%}
{% if annots.length > 0 %}
### Imported on {{importDate | format("YYYY-MM-DD h:mm a")}}

{% for color, annots in annots | groupby("color") -%}
#### {{colorValueToName(color)}}

{% for annot in annots -%}
> [!quote{% if annot.color %}|{{annot.color}}{% endif %}] {{calloutHeader(annot.type)}}
{%- if annot.annotatedText %}
> {{annot.annotatedText | nl2br}}
{%- endif -%}
{%- if annot.imageRelativePath %}
> ![[{{annot.imageRelativePath}}]]
{%- endif %}
{%- if annot.ocrText %}
> {{annot.ocrText}}
{%- endif %}
{%- if annot.comment %}
>
>> {{annot.comment | nl2br}}
{%- endif %}
>
> [Page {{annot.page}}](zotero://open-pdf/library/items/{{annot.attachment.itemKey}}?page={{annot.page}}) ,  {{annot.date | format("YYYY-MM-DD#h:mm a")}}

{% endfor -%}
{% endfor -%}
{% endif %}
{% endpersist %}

