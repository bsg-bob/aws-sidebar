# AWS Sidebar for Chrome

A chrome extension to help you navigate the AWS console a little more efficiently.

Improved navigation of the AWS console.

Features:

- build a custom list of bookmarks to AWS console pages for the services you use, including "deep" links like "EC2 Instances" and "Cloudwatch Logs"
- build a custom list of pricing links for the services you use
- maintain a quick, easy-to-use list of your EC2 instances, with critical properties like IP address
- generate arbitrary hyperlinks to instance-specific information (for example, you could generate a hyperlink to netdata at http://IP-ADDRESS:19999/)

The sidebar will appear as a tab on the right-hand side of your browser when you visit any URL where the hostname matches "aws.amazon.com".

Click on the tab to open the sidebar.  There are three tabs:

- Console - a list of console shortcuts; click on the gear icon to manage the list
- Instances - a filtered list of EC2 instances, with lots of handy hyperlinks
- Pricing - a list of pricing shortcuts; click on the gear icon to manage the list

## Console Panel

By default, you won't see any bookmarks on this panel.  Click the settings button (the gear icon in the lower
right-hand corner).  Select your favorite services and click the "close" button at the top of the modal dialog.
You should now see bookmarks for the selected services.

## Instances Panel

By default, you won't see any instances listed on this panel.  Click the settings button (the gear icon in the lower
right-hand corner).  The settings dialog will open up.

If you want to view your instances, you must at least provide an IAM access key id, a secret access key, and a region.
If any of these is missing, you will not see any instance data.

### Filters

After you have configured the keys and the region, you can fine-tune the list of instances using a tag filter and state filters.

For example, you can limit the list to hosts with a value of "web-server" for the tag "InstanceType" (this example
assumes you use an "InstanceType" tag):
 
```
InstanceType=web-server
```
  
The right-hand side of the expression is interpreted as a regular expression.  So if you wanted to view your 
web servers and your streaming servers:

```
InstanceType=web-server|streaming-server
```
 
You can use multiple filters (AND-ed together), separating them with semicolons:

```
InstanceType=web-server|streaming-server;Stack=production
```

Finally, you can filter based on the state of the instances.  If you check multiple boxes, instances matching
*any* of the states selected will be displayed.

### Hyperlink Generator

The hyperlink generator is the body of a Javascript function (no declaration or surrounding braces)
 that you provide to build an array of hyperlinks
 relevant to your instances.  When you are viewing the list of instances, you can click on an instance
 name to expand out a details panel.  At the bottom, you will have a list of hyperlinks generated by
 this function.
 
For each instance in the list, your function will be passed a variable, `instance`, which would look like the following:

```
{
  "instance_id": "i-fcdf05f3fa7916340",
  "state": "running",
  "instance_type": "t2.small",
  "private_ip_address": "172.32.4.65",
  "launch_time": "2017-07-06T00:40:40.000Z",
  "image_id": "ami-81584ebb",
  "az": "us-east-1b",
  "public_ip_address": "",
  "tags": {
    "Stack": "prod",
    "InstanceType": "automation",
    "Managed": "true"
  },
  "name": "prod-automation",
  "asg": "prod-automation-00942c29ad38258c84492243d1",
  "security_groups": [
    {
      "name": "prod-monitored-instances",
      "id": "sg-04e4bb3a"
    },
    {
      "name": "prod-automation",
      "id": "sg-f4d613a9"
    }
  ]
}
```

Using this information, your function code should build and return a list of hyperlinks (HTML strings).
 
Here is an example function:

```
var links = [];

if (typeof instance.private_ip_address === 'undefined')
{
    return links;
}

var link = "<a href=\"ssh://" + instance.private_ip_address + "/    \">ssh</a>";
links.push (link);

link = "<a href=\"http://" + instance.private_ip_address + ":19999/\" target=\"_blank\">netdata</a>";
links.push (link);

return links;
```

This function builds two links, one to ssh (you can click it and open an ssh session), and one to
a netdata console running on that host.

## Pricing Panel

By default, you won't see any bookmarks on this panel.  Click the settings button (the gear icon in the lower
right-hand corner).  Select your favorite services and click the "close" button at the top of the modal dialog.
You should now see bookmarks for the selected services.
