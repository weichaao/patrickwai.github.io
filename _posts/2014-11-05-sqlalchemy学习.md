---
title: SQLAlchemy学习
layout: post
category: python
---
    Base = declarative_base()
    
    class Author(Base):
      __tablename__ = 'author'
      id = Column(Integer, primary_key=True)
      name = Column(String(512))
      password = Column(String(512))
      authority = Column(Integer)
    
      def __repr__(self):
          return "<Author(name='%s', password='%s')" % (self.name, self.password)
    
    if __name__ == '__main__':
      # The echo flag is a shortcut to setting up SQLAlchemy logging
      # Create an instance of Engine class
      engine = create_engine('mysql://root:root@127.0.0.1:3306/sqlalchemy', echo=False)
      #Create all tables
      Base.metadata.create_all(engine)
    
      # create a class object of Session
      Session = sessionmaker()
      Session.configure(bind=engine)
    
      # Create a session
      session = Session()
    
      author = Author(name='Jack', password='123')
      session.add(author)
    
      #make a query and flush session to database but no committing
      user = session.query(Author).filter_by(name='Jack').first()
      print user.id
    
      session.add_all([
          Author(name='wendy', password='foobar'),
          Author(name='mary', password='xxg527'),
          Author(name='fred', password='blah')])
    
      user.password = 'xxxyyy'
      # display the object which was modified. cause the session is always paying attention.
      print session.dirty
      # display the objects which are pending
      print session.new
    
      # commit flushed all remaining changes to the database and commits the transaction.
      # then this transaction ends.
      session.commit()
    
      # user is persisted
      print user
    
      # Roll back
      user.name = 'Bill'
      another_user = Author(name='Gate', password='password')
      session.add(another_user)
      # flush to database but no committing
      session.flush()
      # rollback.the objects modified will be reverted to the previous state
      # the objects new-added will be discarded
      session.rollback()
      print user.name  # => 'Jack'
      print another_user in session  # => False
