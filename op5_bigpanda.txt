#!/bin/bash

# Follow the directions in the following document. Use the Nagios Notifications setup.
# https://docs.bigpanda.io/docs/nagios

OP5_Server_IP=127.0.0.1
OP5_Server_Username=administrator
OP5_Server_Password=monitor

###################################################
###################################################
######## This is the usermod required for every ###
######## OP5 instance.                          ###
###################################################
###################################################
# usermod -a -G bigpanda monitor
###################################################
###################################################
###################################################

notify_service_data()
{
	cat <<EOF
{
   "command_name":"notify-service-by-bigpanda",
   "command_line":"\/usr\/bin\/bigpanda-notification HOSTOUTPUT=\"$HOSTOUTPUT$\" HOSTSTATE=\"$HOSTSTATE$\" HOSTNAME=\"$HOSTNAME$\" HOSTGROUPNAMES=\"$HOSTGROUPNAMES$\" LASTSERVICESTATECHANGE=\"$LASTSERVICESTATECHANGE$\" LASTHOSTSTATECHANGE=\"$LASTHOSTSTATECHANGE$\" LONGSERVICEOUTPUT=\"$LONGSERVICEOUTPUT$\" LONGHOSTOUTPUT=\"$LONGHOSTOUTPUT$\" NOTIFICATIONTYPE=\"$NOTIFICATIONTYPE$\" SERVICEOUTPUT=\"$SERVICEOUTPUT$\" SERVICEDESC=\"$SERVICEDESC$\" SERVICESTATE=\"$SERVICESTATE$\" SERVICEGROUPNAMES=\"$SERVICEGROUPNAMES$\"",
   "register":true,
   "file_id":"etc\/checkcommands.cfg"
}
EOF
}

notify_host_data()
{
	cat <<EOF
{
   "command_name":"notify-host-by-bigpanda",
   "command_line":"\/usr\/bin\/bigpanda-notification HOSTOUTPUT=\"$HOSTOUTPUT$\" HOSTSTATE=\"$HOSTSTATE$\" HOSTNAME=\"$HOSTNAME$\" HOSTGROUPNAMES=\"$HOSTGROUPNAMES$\" LASTSERVICESTATECHANGE=\"$LASTSERVICESTATECHANGE$\" LASTHOSTSTATECHANGE=\"$LASTHOSTSTATECHANGE$\" LONGSERVICEOUTPUT=\"$LONGSERVICEOUTPUT$\" LONGHOSTOUTPUT=\"$LONGHOSTOUTPUT$\" NOTIFICATIONTYPE=\"$NOTIFICATIONTYPE$\" SERVICEOUTPUT=\"$SERVICEOUTPUT$\" SERVICEDESC=\"$SERVICEDESC$\" SERVICESTATE=\"$SERVICESTATE$\" SERVICEGROUPNAMES=\"$SERVICEGROUPNAMES$\"",
   "register":true,
   "file_id":"etc\/checkcommands.cfg"
}
EOF
}

bigpanda_contact()
{
	cat <<EOF
{
   "contact_name":"bigpanda",
   "alias":"BigPanda Contact",
   "host_notification_options":[
      "d",
      "f",
      "r",
      "s",
      "u"
   ],
   "service_notification_options":[
      "c",
      "f",
      "r",
      "s",
      "u",
      "w"
   ],
   "host_notification_cmds":"notify-host-by-bigpanda",
   "service_notification_cmds":"notify-service-by-bigpanda",
   "register":true,
   "template":"default-contact",
   "file_id":"etc\/contacts.cfg",
   "contactgroups":[
      "bigpanda"
   ],
   "host_notifications_enabled":true,
   "service_notifications_enabled":true,
   "can_submit_commands":true,
   "retain_status_information":true,
   "retain_nonstatus_information":true,
   "host_notification_period":"24x7",
   "service_notification_period":"24x7",
   "host_notification_cmds_args":"",
   "service_notification_cmds_args":"",
   "email":"",
   "pager":"",
   "address1":"",
   "address2":"",
   "address3":"",
   "address4":"",
   "address5":"",
   "address6":"",
   "enable_access":"",
   "realname":"",
   "password":"",
   "password_algo":"",
   "modules":[

   ],
   "groups":[

   ],
   "Access Levels":"",
   "Realname":"",
   "Password":"",
   "Role":[

   ]
}
EOF
}

bigpanda_contactgroup()
{
	cat <<EOF
{
   "contactgroup_name":"bigpanda",
   "alias":"BigPanda",
   "register":true,
   "file_id":"etc\/contactgroups.cfg",
   "members":[
      "bigpanda"
   ],
   "contactgroup_members":[

   ]
}
EOF
}

#Create Service "notifify-service-by-bigpanda"
curl \
-H "content-type: application/json" \
-d "$(notify_service_data)" "https://$OP5_Server_IP/api/config/command" \
-u "$OP5_Server_Username:$OP5_Server_Password"

#Create Service "notify-host-by-bigpanda"
curl \
-H "content-type: application/json" \
-d "$(notify_host_data)" "https://$OP5_Server_IP/api/config/command" \
-u "$OP5_Server_Username:$OP5_Server_Password"

#Create contact "bigpanda"
curl \
-H "content-type: application/json" \
-d "$(bigpanda_contact)" "https://$OP5_Server_IP/api/config/contact" \
-u "$OP5_Server_Username:$OP5_Server_Password"

#Create contactgroup "bigpanda"
curl \
-H "content-type: application/json" \
-d "$(bigpanda_contactgroup)" "https://$OP5_Server_IP/api/config/contactgroup" \
-u "$OP5_Server_Username:$OP5_Server_Password"