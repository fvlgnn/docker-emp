FROM mariadb:10.3


COPY mariadb.cnf /etc/mysql/mariadb.conf.d/mariadb.cnf

CMD ["--innodb-flush-method=fsync", "--ignore-db-dir=lost+found", "--explicit_defaults_for_timestamp", "--skip-character-set-client-handshake"]

