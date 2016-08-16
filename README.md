# percona-presentation
This repository contains all accompanying material for the [Dubytes Percona Meetup](https://www.meetup.com/Dubytes/events/232841854/) held at dubizzle HQ, Dubai. It includes the following:

- Dockerfile to build an image for MySQL 5.6<sup>[1]</sup> and configure Percona Toolkit<sup>[2]</sup> on that.
- A minimal Django website<sup>[3]</sup> that allows you to create and delete records.

Presentation link: https://docs.google.com/presentation/d/1lQ6I948TfzMfYzVDb0QwFxzubH3BbTTt_gxijzUcBC0/edit?pref=2&pli=1#slide=id.p

Blog link: http://blog.dubizzle.com/boilerroom/2016/06/13/live-mysql-schema-changes-amazon-rds-percona-walkthrough/

## Setup:

Go to mysql dir and run:

```
docker build -t mysql:5.6 .
```

Make sure that env variable `PWD` is setup on your terminal. If not, set it to the absolute path of current working dir, in this case mysql.

Then run:

```
docker run -p 3306:3306 --name percona-playground -v $PWD/conf:/etc/mysql/conf.d -v $PWD/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=pass -e MYSQL_DATABASE=percona_playground mysql:5.6
```
Note that we mount datadir to make MySQL data persist across different runs of the docker image.

To go inside the container:

```
docker exec -it percona-playground bash
```

To go inside mysql shell from container:

```
mysql -uroot -ppass
```

You would find Percona Toolkit pre-installed inside the cointainer so you can start playing around. Following is one of the `pt-online-schema-change` commands to let you start with:

```
pt-online-schema-change --dry-run --chunk-size=2000 --alter-foreign-keys-method=rebuild_constraints --alter 'add column other_id INT DEFAULT NULL' h=localhost,D=percona_playground,t=snippets_snippet -ppass
```
*Note: If you are familiar with Django, you can also utilize the sample Django project and run `python manage.py migrate` to quickly get a few tables setup for you. It is configured with all the DB credentials to work out of the box.*

----
[1] MySQL 5.6 Dockerfile borrowed from: https://github.com/docker-library/mysql/blob/master/5.6/Dockerfile

[2] Percona Toolkit Dockerfile borrowed from: https://github.com/fr3nd/docker-percona-toolkit/blob/master/Dockerfile

[3] Django sample website borrowed from: http://www.django-rest-framework.org/tutorial/1-serialization/
