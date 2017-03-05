# zoomeyer
This is a program to use Zoomeye.org's API for exploering the IOT.
Before using  this program, you are expected to register an account at https://sso.telnet404.com/accounts/register/.
And remember, your username must be an actived e-mail address. 

When your Zoomeye's account is ready, it's time for Zoomeyer!

X:./zoomeyer

You will see a welcome pic, then type in your username(e-mail) and password, press ENTER.
If your network & account is OK, you will see your role and searching times you have.
Commonly, a new account's role is "develpoer", and it can make you search both hosts & webs for 5000 times.
For detail, please visit https://www.zoomeye.org/api/doc#visiblity

Then, you will be asked to choose a type you want to search.
The typical answer is 'hosts'(for host on the Internet) or 'sites'(for web servers and so on.)
NOTE:You'd better not answer other words, except you want to restart the program.
Let's take 'hosts' for example:

X:Which kind of device?>hosts

X:Please enter the search keyword:>SONY

Then you will see brief searching result page by page.
Everytime the result page printed, there will be an 'MORE:>' on the bottom of the screen.
If you type in the number of the result you want like this:

MORE:>7

then you will get the host's information in detail.
Else if you type in nothing but enter:

MORE:>

then you will get the next page.
Else if you type in other letters like this:

MORE:>no

then the program will exit.

The excution of searching web servers is similar.

I can't promise there is no bugs in the code, 
so if you find that the program runs badly, 
or you have something better than mine, 
please e-mail to vichi_7@sina.com.


