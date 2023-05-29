---
{"dg-publish":true,"dg-path":"Cheat sheets/AWS cheat sheet.md","permalink":"/cheat-sheets/aws-cheat-sheet/"}
---


# Delete stuck CloudFormation stack

- If you get a *Failed to delete stack: Role {role} is invalid or cannot be assumed* error when trying to delete a stack:
    - create a new role with type **AWS Service**, use case **CloudFormation**, permission **AdministratorAccess**, with the name of the role the stuck stack used (the part after `:role/`)
        - may take a few seconds for the role to propagate to CloudFormation
    - if there are any resources left in the **Resources** tab that were already deleted (such as a Lambda function), try the delete again and check the box to retain them

# Export Route 53 DNS zonefile using cli53

```shell
cli53 export --full --debug example.com > example.com.zone 2> example.com.zone.log
```

<div class="rich-link-card-container"><a class="rich-link-card" href="https://stackoverflow.com/a/58358563" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://cdn.sstatic.net/Sites/stackoverflow/Img/apple-touch-icon@2.png?v=73d79a89bded')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Exporting DNS zonefile from Amazon Route 53</h1>
		<p class="rich-link-card-description">
		I would like to export a DNS zonefile from my Amazon Route 53 setup. Is this possible, or can zonefiles only be created manually? (e.g. through http://www.zonefile.org/?lang=en)
		</p>
		<p class="rich-link-href">
		https://stackoverflow.com/a/58358563
		</p>
	</div>
</a></div>
