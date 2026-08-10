## Add a new service account on the DC ##

folow these steps:

1: On the DC, open server Server Manager → Tools menu (top right) → Active Directory Users and Computers.
2: Expand ad-lab.local (click the arrow next to it). You'll see folders like Users, Computers, etc. 
Right-click on the Users folder → New → User.
3: Type the service account name and User logon name, Type it in, click Next.
4: Type in the new user's password and uncheck "User must change password at next logon". click Next. 
5: click finish and there you have it. 

Expand the users folder and verefy that your new service accout really did get created.