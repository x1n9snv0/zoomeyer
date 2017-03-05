#!/usr/bin/env python
import sys
import urllib2
import json
import math
import os
import getpass


class Zoomeyer:
    def __init__(self):
        self._auth_url = 'https://api.zoomeye.org/user/login'
        self._resource_url = 'https://api.zoomeye.org/resources-info'
        self._host_search_url = 'https://api.zoomeye.org/host/search?'
        self._web_search_url = 'https://api.zoomeye.org/web/search?'
        self.temp_file = '/tmp/zoomeye.tmp'
        self._auth = ''

    def login(self, username, password):
        auth_post_data = {"username": username, "password": password}
        json_auth_post_data = json.dumps(auth_post_data)
        req = urllib2.Request(self._auth_url)
        try:
            res = urllib2.urlopen(req, json_auth_post_data)
            if res.getcode() == 200:
                print "Login success."
                auth = res.read()[18:-2]
                self._auth = auth
                self.get_resources_info()
        except urllib2.HTTPError as e:
            print "Login failed."
            print str(e)
            sys.exit(401)

    def get_resources_info(self):
        get_resources_request = urllib2.Request(self._resource_url)
        get_resources_request.add_header("Authorization", "JWT "+self._auth)
        get_resources_response = urllib2.urlopen(get_resources_request)
        if get_resources_response.getcode() == 200:
            plan = json.loads(get_resources_response.read())
            print ("plan:"+plan['plan'])
            print ("host-search:"+plan['resources']['host-search'])
            print ("web-search:" +plan['resources']['web-search'])
        else:
            print "You are offline."

    def query(self, destination, query, page='1'):
        query_request = urllib2.Request
        if destination == 'host':
            query_request = urllib2.Request(self._host_search_url + "query=" + query + "&page=" + page)
        elif destination == 'sites':
            query_request = urllib2.Request(self._web_search_url + "query=" + query + "&page=" + page)
        else:
            print "Unknown query parameter."
        query_request.add_header("Authorization", "JWT " + self._auth)
        try:
            query_response = urllib2.urlopen(query_request)
            return query_response
        except urllib2.HTTPError as e:
            print str(e)
            sys.exit(500)

    def search_hosts(self, query):
        query_response = self.query('host', query)
        if query_response.getcode() == 200:
            print "Request success."
            h_response = json.loads(query_response.read())
            print ("Find "+str(h_response['total'])+" hosts.")
            total = h_response['total']
            total_page = int(math.ceil(total/10.0))
            for p in range(total_page-1):
                query_response = self.query('host', query, page=str(p+1))
                if query_response.getcode() == 200:
                    h_response = json.loads(query_response.read())
                    try:
                        temp = open(self.temp_file+str(p+1), 'a')
                        temp.write(json.dumps(h_response))
                        temp.close()
                    except IOError as e:
                        print e
                    print "+-"*11+"Page: "+str(p+1)
                    i = 1
                    for hosts in h_response['matches']:
                        print 'Number: '+str(p*10+i)
                        print ("IP: "+hosts['ip'])
                        print ("PORT: "+str(hosts['portinfo']['port']))
                        print ("Country: "+hosts['geoinfo']['country']['names']['en'])
                        print "*"*21
                        i += 1
                    cmd = raw_input('MORE:> ')
                    if cmd == '':
                        continue
                    else:
                        try:
                            target = int(cmd)
                            while 1:
                                if target <= p*10+10:
                                    if target % 10 == 0:
                                        temp = open(self.temp_file+str(target/10), 'r')
                                    else:
                                        temp = open(self.temp_file+str(target/10+1), 'r')
                                    temp_data = json.load(temp)
                                    index = target % 10
                                    if index == 0:
                                        index = 10
                                    print "<"*81
                                    print ("IP: " +
                                           temp_data['matches'][index - 1]['ip'])
                                    print ("PORT: " +
                                           str(temp_data['matches'][index - 1]['portinfo']['port']))
                                    print ("Country: " +
                                           temp_data['matches'][index - 1]['geoinfo']['country']['names']['en'])
                                    print ("Location: (" +
                                           str(temp_data['matches'][index - 1]['geoinfo']['location']['lat']) +
                                           ", " +
                                           str(temp_data['matches'][index - 1]['geoinfo']['location']['lon']) + ")")
                                    print ("Banner: \r\n" +
                                           temp_data['matches'][index - 1]['portinfo']['banner'])
                                    print ">"*81
                                    temp.close()
                                    cmd = raw_input('MORE> ')
                                    target = int(cmd)
                                else:
                                    print "You ordered an unreached number."
                                    break
                        except ValueError:
                            if cmd == '':
                                continue
                            else:
                                break
                else:
                    print query_response.info()
                    break
        elif query_response.getcode() == 400:
            print "Lack of 'query' parameter."
        elif query_response.getcode() == 422:
            print "Unsupportable parameter inside."
        else:
            sys.exit(1)

    def search_sites(self, query):
        query_response = self.query('sites', query)
        if query_response.getcode() == 200:
            print "Request success."
            s_response = json.loads(query_response.read())
            print ("Find "+str(s_response['total'])+" sites.")
            total = s_response['total']
            total_page = int(math.ceil(total/10.0))
            for p in range(total_page-1):
                query_response = self.query('sites', query, page=str(p+1))
                if query_response.getcode() == 200:
                    s_response = json.loads(query_response.read())
                    try:
                        temp = open(self.temp_file+str(p+1), 'a')
                        temp.write(json.dumps(s_response))
                        temp.close()
                    except IOError as e:
                        print e
                    print "+-"*11+"Page: "+str(p+1)
                    i = 1
                    for sites in s_response['matches']:
                        print 'Number: '+str(p*10+i)
                        print ("Site: "+sites['site'])
                        print ("IP: \r")
                        for ip in sites['ip']:
                            print ("    "+ip)
                        print ("Server: \r")
                        for server in sites['server']:
                            print ("    " + server['name'])
                        print ("Country: "+sites['geoinfo']['country']['names']['en'])
                        print ("City: "+sites['geoinfo']['city']['names']['en'])
                        print "*"*21
                        i += 1
                    cmd = raw_input('MORE:> ')
                    if cmd == '':
                        continue
                    else:
                        try:
                            target = int(cmd)
                            while 1:
                                if target <= p*10+10:
                                    if target%10 == 0:
                                        temp = open(self.temp_file+str(target/10), 'r')
                                    else:
                                        temp = open(self.temp_file+str(target/10+1), 'r')
                                    temp_data = json.load(temp)
                                    index = target % 10
                                    if index == 0:
                                        index = 10
                                    print "<"*81
                                    print ("Title: " + temp_data['matches'][index - 1]['title'])
                                    print ("Description: " + temp_data['matches'][index - 1]['description'])
                                    print ("Site: " +
                                           temp_data['matches'][index - 1]['site'])
                                    print ("Country: " +
                                           temp_data['matches'][index - 1]['geoinfo']['country']['names']['en'])
                                    print ("Location: (" +
                                           str(temp_data['matches'][index - 1]['geoinfo']['location']['lat']) +
                                           ", " +
                                           str(temp_data['matches'][index - 1]['geoinfo']['location']['lon']) + ")")
                                    print ("IP: \r")
                                    for ip in temp_data['matches'][index - 1]['ip']:
                                        print ("    " + ip)
                                    print ("Language: \r")
                                    for language in temp_data['matches'][index - 1]['language']:
                                        print ("    " + language)
                                    print ("Webapp: \r")
                                    for webapp in temp_data['matches'][index - 1]['webapp']:
                                        try:
                                            sys.stdout.write("    " + webapp['url'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(', ' + webapp['name'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(' ' + webapp['version'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        sys.stdout.write("\n")
                                        sys.stdout.flush()
                                    print ("Server: \r")
                                    for server in temp_data['matches'][index - 1]['server']:
                                        try:
                                            sys.stdout.write("    " + server['name'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(" " + server['version'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        sys.stdout.write("\n")
                                        sys.stdout.flush()
                                    print ("Database: \r")
                                    for db in temp_data['matches'][index - 1]['db']:
                                        try:
                                            sys.stdout.write("    " + db['name'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(" " + db['version'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        sys.stdout.write("\n")
                                        sys.stdout.flush()
                                    print ("Components: \r")
                                    for component in temp_data['matches'][index - 1]['component']:
                                        try:
                                            sys.stdout.write("    " + component['name'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(" " + component['version'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        sys.stdout.write("\n")
                                        sys.stdout.flush()
                                    print ("Domains: \r")
                                    for domain in temp_data['matches'][index - 1]['domains']:
                                        print ("    " + domain)
                                    print ("System: \r")
                                    for system in temp_data['matches'][index - 1]['system']:
                                        try:
                                            sys.stdout.write("    " + system['name'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(" " + system['distrib'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        try:
                                            sys.stdout.write(" " + system['release'])
                                            sys.stdout.flush()
                                        except TypeError:
                                            pass
                                        sys.stdout.write("\n")
                                        sys.stdout.flush()
                                    print ("Headers: \n" + temp_data['matches'][index - 1]['headers'])
                                    print ">"*81
                                    temp.close()
                                    cmd = raw_input('MORE> ')
                                    target = int(cmd)
                                else:
                                    print "You ordered an unreached number."
                                    break
                        except ValueError:
                            if cmd == '':
                                continue
                            else:
                                break
                else:
                    print query_response.info()
                    break
        elif query_response.getcode() == 400:
            print "Lack of 'query' parameter."
        elif query_response.getcode() == 422:
            print "Unsupportable parameter inside."
        else:
            sys.exit(1)


def main():
    zoomeyer = Zoomeyer()
    print "******************************************************************"
    print " zzzzzzz                                                          "
    print "     zz                                                           "
    print "    zz    oooo   oooo   mmmm mmmm   eeee  yyy   yy  eeee   rr  rr "
    print "   zz    oo  oo oo  oo mm  mmm  mm ee  ee   yy yy  ee  ee  rr rr  "
    print "  zz     oo  oo oo  oo mm  mmm  mm eeee      yyy   eeee    rrr    "
    print " zzzzzzz  0oo0   oooo  mm  mmm  mm  eeeeee   yy     eeeeee rr     "
    print "                                            yy                    "
    print "                                           yy                     "
    print "  Product by Mr.Q                       yyyy                      "
    print "******************************************************************"
    print "---------Welcome to the zoomeyer, you should login first.---------"
    username = raw_input("Username: ")
    password = getpass.getpass("Password: ")
    zoomeyer.login(username, password)
    destination = raw_input("Which kind of device?>")
    keyword = raw_input('Please enter the search keyword:>')
    if destination == 'hosts':
        zoomeyer.search_hosts(keyword)
    elif destination == 'sites':
        zoomeyer.search_sites(keyword)
    else:
        print "Destination unreachable."
    i = 1
    while 1:
        try:
            os.remove(zoomeyer.temp_file+str(i))
            i += 1
        except OSError:
            break

if __name__ == '__main__':
    main()
