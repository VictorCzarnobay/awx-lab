# awx-lab
Teste AWX

# NGINX Playbook

---
- hosts: all
  tasks:
    - name: Ensure Nginx is installed and it is the latest version
      apt:
        name: nginx
        state: latest
        become: yes
   
    - name: Start Nginx
      service:
        name: nginx
        state: started
      become: yes
    
    - name: Copy the Nginx config file and restart nginx
      copy:
        src: ./nginx.cfg
        dest: /etc/nginx/sites-available/nginx.cfg
      become: yes
     
    - name: Create symlink
      file:
        src: /etc/nginx/sites-available/nginx.cfg
        dest: /etc/nginx/sites-enabled/default
        state: link
      become: yes

    - name: Restart nginx
      service:
        name: nginx
        state: restarted
      become: yes


      # ----------------------------------------------------------------------------------------------

      # NGINX config file

      upstream backend {
  server 192.168.33.12:5000; #server-1
  server 192.168.33.13:5000; #server-2
}

server {
  listen 80; #listen on port 80
  server_name web.app;
  location / {
    proxy_read_timeout 300s;
    proxy_pass http://backend; #pass all requests processed to the backend upstream servers
  }
}
