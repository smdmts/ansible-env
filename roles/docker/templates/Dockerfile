FROM {{ docker_from }}

RUN yum -y update

RUN yum -y install openssh-server sudo

# Create user
RUN useradd docker
RUN passwd -f -u docker

# Set up SSH
RUN mkdir -p /home/docker/.ssh; chown docker /home/docker/.ssh; chmod 700 /home/docker/.ssh
ADD authorized_keys /home/docker/.ssh/authorized_keys

RUN chown docker /home/docker/.ssh/authorized_keys
RUN chmod 600 /home/docker/.ssh/authorized_keys

# setup sudoers
RUN echo "docker ALL=(ALL) ALL" >> /etc/sudoers.d/docker

# Set up SSHD config
RUN sed -ri 's/#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config

RUN echo "NETWORKING=yes" > /etc/sysconfig/network

RUN /etc/init.d/sshd start

EXPOSE 22

CMD ["/usr/sbin/sshd","-D"]


# MySql
RUN yum install -y mysql mysql-server

# MySql server setup
RUN echo "NETWORKING=yes" > /etc/sysconfig/network
ADD ./setup.sql
RUN /usr/bin/mysqld_safe & \
        sleep 10s && \
        cat setup.sql | mysql
RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

EXPOSE 3306

CMD ["/usr/bin/mysqld_safe"]
