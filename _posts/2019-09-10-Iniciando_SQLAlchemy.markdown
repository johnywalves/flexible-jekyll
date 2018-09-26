---
layout: post
title: "Iniciando com SQLAlchemy (Python)"
date: 2018-09-10 20:33:15 -0300
tags: [SQLAlchemy, Python, "Serialização"]
img: alchemy-1.jpg
output: html_document      
---



Instalar o pacote do SQLAlchemy pelo repositório


{% highlight bash %}
pip install sqlalchemy
{% endhighlight %}


{% highlight python %}
engine = create_engine('sqlite:///mydb.db', poolclass=SingletonThreadPool)
{% endhighlight %}


{% highlight python %}
Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
session = Session()
{% endhighlight %}


{% highlight python %}
class User(Base):

    __tablename__ = 'users'

    id          =   Column(Integer, primary_key=True)
    user        =   Column(String(50), nullable=False)
    password    =   Column(String(50), nullable=False)    

    def __repr__(self):
        return "<User (user='%d')>" % (self.user)
{% endhighlight %}

### Referências 

[First steps with SQLAlchemy](https://bytefish.de/blog/first_steps_with_sqlalchemy/)
