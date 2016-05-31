from flask import Flask, render_template, request, redirect, session, url_for, \
    abort, g, flash
import requests
import json
import os
import sqlite3
from flask.ext.bootstrap import Bootstrap
from datetime import datetime
from flask.ext.wtf import Form
from wtforms import StringField, SubmitField
from wtforms.validators import Required
from flask.ext.sqlalchemy import SQLAlchemy
from flask.ext.script import Shell

class NameForm(Form):
    name = StringField('What\'s your Name?', validators=[Required()])
    submit = SubmitField('Submit')


app = Flask(__name__)
bootstrap = Bootstrap(app)
basedir = os.path.abspath(os.path.dirname(__file__))

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + os.path.join(basedir, 'data.sqlite')
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
app.config['SECRET_KEY'] = os.environ['KEY']

db = SQLAlchemy(app)

class Role(db.Model):
    __tablename__ = 'roles'
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(64), unique=True)
    users = db.relationship('User', backref='role')

    def __repr__(self):
        return '<Role %r>' % self.name

class User(db.Model):
    __tablenames__ = 'users'
    id = db.Column(db.Integer, primary_key = True)
    username = db.Column(db.String(64), unique = True)
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))

    def __repr__(self):
        return '<User %r>' % self.username


class Book(db.Model):
    __tablename__ = 'books'
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.Integer, unique=False)



@app.route('/helloworld')
def helloworld():
    return "Hello, World"


@app.route('/helloworld/<name>')
def helloname(name):
    return "Hello, %s" % (name)


@app.route('/user/', methods=['GET', 'POST'])
def user():
    form = NameForm()
    if form.validate_on_submit():
        user = User.query.filter_by(username=form.name.data).first()
        if user is None:
            user = User(username= form.name.data)
            db.session.add(user)
            session['known'] = False
        else:
            session['known'] = True
        session['name'] = form.name.data
        form.name.data = ''
        return redirect(url_for('user'))
    return render_template('user.html',
            name=session.get('name'),
            form=form,
            current_time=datetime.utcnow(),
            known=session.get('known', False))


@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404


@app.errorhandler(500)
def internal_server_error(e):
    return render_template('500.html'), 500






# configuartion
DATABASE = 'flaskr.db'
DEBUG = True
SECRET_KEY = 'development key'
USERNAME = 'admin'
PASSWORD = 'default'
APP_NAME = 'flaskr'

app.config.from_object(__name__)

def connect_db():
    return sqlite3.connect(app.config['DATABASE'])

@app.before_request
def before_request():
    g.db = connect_db()


@app.route('/flaskr')
def flaskr():
    conn = g.db.get_cursor()
    conn.execute('''select * from entries''')
    result = conn.fetchall()
    return render_template('flaskr.html',title=result[0][1])



@app.route('/routine')
def routine():
    tasks = [
        ('Fix your broken windows',2),
        ('Weigh yourself every day', 1),
        ('Practice Rebounding', 3),
        ('16oz Glass of Cold Water with Lemon', 2),
        ('Take a Daily Vitamins', 2),
        ('Maintain a Food Journal', 5),
        ('Review Your Goals', 5),
        ('Use the Lift App', 5),
        ('Let the Natual Light in', 1),
        ('Make an Antioxidant Smoothie', 3),
        ('Eat Other Nutritious Foods', 5),
        ('Meditate for 5 Minutes', 5),
        ('Yoga for 10 minutes', 10),
        ('Have a Mint', 2),
        ('Have a Jam Session', 4),
        ('Text Something Encouraging', 2),
        ('Leave a note for your loved one',2),
        ('Recite Affirmations', 3),
        ('Read your favorite blog or a chapter in your newest book', 5),
        ('Read an Inspirational Passage', 5),
        ('Sign into an Online Forum or Facebook Group', 5),
        ('Create a Most Important Tasks (MITs) List', 5),
        ('Plan your run', 5),
        ('Keep it regular', 5),
        ('Reward Yourself Immediately', 5),
        ('Build your own support system', 5)]

    return render_template('routine.html', tasks=tasks)


@app.route('/frogs')
def frog_home():
    return render_template('home.html')


@app.route('/frogs/books')
def frog_books():
    return render_template('books.html')


@app.route('/frogs/membership_card')
def frog_membership_card():
    return render_template('membership_card.html')


@app.route('/frogs/snacks')
def frog_snacks():
    return render_template('snacks.html')


@app.route('/frogs/quiz_main')
def frog_quiz_main():
    return render_template('quiz-main.html')


@app.route('/frogs/quiz_1')
def frog_quiz_1():
    return render_template('quiz-1.html')


@app.route('/frogs/quiz_2')
def frog_quiz_2():
    return render_template('quiz-2.html')


@app.route('/frogs/quiz_3')
def frog_quiz_3():
    return render_template('quiz-3.html')











































































class Library:

    def __init__(self):
        self.upc_list = []
        self.API_KEY = os.environ['KEY']

    def get_upc(self, upc):
        self.upc_list.append(upc)

    def lookup_upc(self, upc, db_name):
        outpan_path = "https://api.outpan.com/v2/products/" + upc + "?apikey=" + self.API_KEY
        print(outpan_path)
        response = requests.get(outpan_path)
        print(response.text)
        response = json.loads(response.text)

        title = None
        if 'name' in response:
            title = response['name']
        if 'images' in response:
            image_link = ''
            if len(response['images']) > 0:
                image_link = response['images'][0]
        if 'attributes' in response:
            author = ''
            book_format = ''
            isbn_10 = ''
            publish_date = ''
            publisher = ''
            page_count = ''
            if 'Author(s)' in response['attributes']:
                author = response['attributes']['Author(s)']
            if 'Format' in response['attributes']:
                book_format = response['attributes']['Format']
            if 'ISBN-10' in response['attributes']:
                isbn_10 = response['attributes']['ISBN-10']
            if 'Publication Date' in response:
                publish_date = str(response['attributes']['Publication Date'])
            if 'Publisher' in response['attributes']:
                publisher = response['attributes']['Publisher']
            if 'Page Count' in response['attributes']:
                page_count = response['attributes']['Page Count']

        checked_out = 1
        available = "Y"
        borrower = ""

        if title:
            self.add_book(title, author, isbn_10, image_link, publish_date, publisher, page_count, book_format, db_name, checked_out, available, borrower)
            self.view_books()
            return [title, author, isbn_10, image_link, publish_date, publisher, page_count, book_format]
        else:
            print("Not Found")
            return ["Not Found","","","","","","",""]

    def list_upcs(self):
        for i in self.upc_list:
            print(i)

    def add_book(self, title, author, isbn_10, image_link, publish_date, publisher, page_count, book_format, db_name, checked_out, available, borrower):
        if db_name == 'lexi':
            dbc = sqlite3.connect('library.db')
        elif db_name == 'abby':
            dbc = sqlite3.connect('library_abby.db')
        c = dbc.cursor()
        c.execute('''
        insert into books(title,author,format,isbn_10,page_count,publish_date,publisher,image_link, checked_out, available, borrower)
        values (?,?,?,?,?,?,?,?,?,?,?)
        ''', (title, author, book_format, isbn_10, page_count, publish_date, publisher, image_link, checked_out, available, borrower))
        dbc.commit()
        dbc.close()

    def view_books(self):
        dbc = sqlite3.connect('library.db')
        c = dbc.cursor()
        c.execute('''select * from books''' )
        for i in c.fetchall():
            print(i)

        dbc.close()



ALLOWED_EXTENSIONS = ['jpg','jpe','png',]

app.debug = True


def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1] in ALLOWED_EXTENSIONS


@app.route('/getPicture', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['file']
        if f and allowed_file(f.filename):
            filename = f.filename
            print(f.filename)
            print(url_for('static',filename=filename))
            f.save("./static/" + filename)
            return redirect(url_for('home'))
    return redirect(url_for('home'))


@app.route('/uploads/<filename>')
def uploaded_file(filename):
    return send_from_directory(app.config['UPLOAD_FOLDER'], filename)


@app.route('/GetImageLink/<ISBN>', methods=['GET','POST'])
def GetImageLink(ISBN):
    path = "http://www.isbnsearch.org/isbn/0763648531"
    response = requests.get(path)
    return response.text



@app.route('/addCoverPicutre', methods=['POST'])
def add_cover_picture():
    image_url = request.form['url']
    book_id = request.form["newImageID"]
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute("update books set image_link =? where id =?;",(image_url, book_id))
    dbc.commit()
    c.close()
    return redirect(url_for('home'))






@app.route('/library')
def home(name='Lexi'):
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute("select * from books where available=? order by title;",('Y',))
    book_data = c.fetchall()
    c.execute('''select * from books order by id desc limit 1''')
    current_book_data = c.fetchone()
    c.execute("select * from books where available=? order by title;",('N',))
    checked_out_books = c.fetchall()
    c.execute('''select count(*) from books where image_link != "" ''')
    count_books = c.fetchone()[0]
    c.execute('''select count(*) from books where image_link = "" ''')
    count_books_no_pic = c.fetchone()[0]
    c.execute(''' select count(*) from books; ''')
    total_books = c.fetchone()[0]
    dbc.close()
    return render_template('index.html', msg='', bookData=book_data,
                                         checkedOutBooks=checked_out_books,
                                         getPassword=False,
                                         currentBook=current_book_data,
                                         count_of_books=count_books,
                                         count_books_no_pic=count_books_no_pic,
                                         total_books = total_books)

@app.route('/')
def go_to_open():
    return redirect(url_for('home'))

@app.route('/SummerVacation', methods=['GET','POST'])
@app.route('/SummerVacation/<user>/<month>/<day>', methods=['GET','POST'])
def summer_vacation(user="lexi", month="May", day="1"):
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute('''select * from summer_reading where month=? and day=? and user=?;''', (month, day, user))
    todays_books = c.fetchall()
    c.execute('''select count(book_title) from summer_reading''')
    total_books_read = c.fetchone()
    c.execute('''select month, day, count(book_title) from summer_reading where user=? and month=? group by month, day;''', (user, month))
    book_counts = c.fetchall()
    c.execute('''select title, image_link from books group by title, image_link;''')
    library_books = c.fetchall()
    dbc.close()
    if month == "June":
        days_list = [range(3),range(1,31)]
    elif month == "July":
        days_list = [range(5),range(1,32)]
    elif month == "August":
        days_list = [range(1),range(1,32)]
    elif month == "May":
        days_list = [range(0), range(1, 32)]
    return render_template('summer_vacation.html',
                            day_count=days_list,
                            month=month,
                            day=day,
                            todays_books=todays_books,
                            total_books_read=total_books_read,
                            book_counts=book_counts,
                            library_books=library_books)



@app.route('/SummerVacation/addBook/<user>/<month>/<day>', methods=['POST'])
def add_summer_vacation_book(user='lexi', month='June', day='1'):
        dbc = sqlite3.connect('library.db')
        c = dbc.cursor()
        title = request.form['book_title']
        comment = request.form['comment']
        c.execute('''
        insert into summer_reading(book_title, comment, user, month, day, year)
        values (?,?,?,?,?,?)
        ''', (title, comment, user, month, day, '2016'))
        dbc.commit()
        dbc.close()
        return redirect('/SummerVacation/%s/%s/%s' % (user, month, day))


@app.route('/SummerVacation/removeBook/<user>/<month>/<day>/<id>', methods=['POST'])
def remove_summer_vacation_book(user='lexi', month='June', day='1', id=''):
        dbc = sqlite3.connect('library.db')
        c = dbc.cursor()
        c.execute('''delete from summer_reading where id=?;''' , (int(str(id)),))
        dbc.commit()
        dbc.close()
        return redirect('/SummerVacation/%s/%s/%s' % (user, month, day))


@app.route('/GetPassword', methods=['POST'])
def getPassword():
    current_book_id = request.form['deleteID']
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute('''select * from books order by title;''')
    book_data = c.fetchall()
    c.execute('select * from books where id="' + str(current_book_id) + '";')
    current_book_data = c.fetchone()
    dbc.close()
    return render_template('index.html',
                        msg = '',
                        bookData=book_data,
                        getPassword=True,
                        currentBook=current_book_data)


@app.route('/CheckOutBook', methods=['POST'])
def checkOutBook():
    current_book_id = request.form['checkOut']
    borrower = request.form['bName']
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute('''update books SET available=? where id=?''',('N',str(current_book_id)))
    dbc.commit()
    c.execute('''update books SET borrower=? where id=?''',(borrower,str(current_book_id)))
    dbc.commit()
    dbc.close()
    return redirect('/', code=302)


@app.route('/CheckIn', methods=['POST'])
def checkInBook():
    current_book_id = request.form['checkIn']
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute('''update books SET available=? where id=?''',('Y',str(current_book_id)))
    dbc.commit()
    c.execute('''update books SET borrower=? where id=?''',('',str(current_book_id)))
    dbc.commit()
    dbc.close()
    return redirect('/', code=302)


@app.route('/EnterBook')
def newbook():
    dbc = sqlite3.connect('library.db')
    c = dbc.cursor()
    c.execute('''select * from books order by title;''')
    book_data = c.fetchall()
    dbc.close()
    return render_template('index.html', msg='', bookData=book_data, enterNewBook=True,
                            currentBook=('','','','','','','',''))


@app.route('/SubmitNewBook', methods=['POST'])
def submitnewbook():
    isbn = request.form['ISBN']
    l = Library()
    new_book_data = l.lookup_upc(isbn, 'lexi')
    if new_book_data[0] != 'Not Found':
        return redirect('/', code=302)
    else:
        dbc = sqlite3.connect('library.db')
        c = dbc.cursor()
        c.execute('''select * from books order by title;''')
        book_data = c.fetchall()
        c.execute('''select * from books order by id desc''')
        current_book_data = c.fetchone()
        dbc.close()
        msg = 'Not Found'
        return render_template('index.html', msg=msg, bookData=book_data, getPassword=False, currentBook=current_book_data)


@app.route('/RemoveBook', methods=['POST'])
def removebook():
    if request.form['password'] == os.environ['DELETE_PASSWORD']:
        dbc = sqlite3.connect('library.db')
        c = dbc.cursor()
        book_id = request.form['bID']
        c.execute('delete from books where id=' + str(book_id) + ';')
        dbc.commit()
        dbc.close()
    return redirect("/", code=302)


########################################### ABBY'S PART #################

@app.route('/Abby')
def homeAbby():
    dbc = sqlite3.connect('library_abby.db')
    c = dbc.cursor()
    c.execute("select * from books where available=? order by title;",('Y',))
    book_data = c.fetchall()
    c.execute('''select * from books order by id desc limit 1''')
    current_book_data = c.fetchone()
    c.execute("select * from books where available=? order by title;",('N',))
    checked_out_books = c.fetchall()
    c.execute('''select count(*) from books where image_link != "" ''')
    count_books = c.fetchone()[0]
    c.execute('''select count(*) from books where image_link = "" ''')
    count_books_no_pic = c.fetchone()[0]
    c.execute(''' select count(*) from books; ''')
    total_books = c.fetchone()[0]
    dbc.close()
    return render_template('index_abby2.html', msg='', bookData=book_data,
                                         checkedOutBooks=checked_out_books,
                                         getPassword=False,
                                         currentBook=current_book_data,
                                         count_of_books=count_books,
                                         count_books_no_pic=count_books_no_pic,
                                         total_books = total_books)

@app.route('/GetPasswordAbby', methods=['POST'])
def getPasswordAbby():
    current_book_id = request.form['deleteID']
    dbc = sqlite3.connect('library_abby.db')
    c = dbc.cursor()
    c.execute('''select * from books order by title;''')
    book_data = c.fetchall()

    c.execute('select * from books where id="' + str(current_book_id) + '";')
    current_book_data = c.fetchone()
    dbc.close()
    return render_template('index_abby2.html',
                        msg = '',
                        bookData=book_data,
                        getPassword=True,
                        currentBook=current_book_data)


@app.route('/EnterBookAbby')
def newbookAbby():
    dbc = sqlite3.connect('library_abby.db')
    c = dbc.cursor()
    c.execute('''select * from books order by title;''')
    book_data = c.fetchall()
    dbc.close()
    return render_template('index_abby2.html', msg='', bookData=book_data, enterNewBook=True,
                            currentBook=('','','','','','','',''))


@app.route('/SubmitNewBookAbby', methods=['POST'])
def submitnewbookAbby():
    isbn = request.form['ISBN']
    l = Library()
    new_book_data = l.lookup_upc(isbn, 'abby')
    if new_book_data[0] != 'Not Found':
        return redirect('/Abby', code=302)
    else:
        dbc = sqlite3.connect('library_abby.db')
        c = dbc.cursor()
        c.execute('''select * from books order by title;''')
        book_data = c.fetchall()
        c.execute('''select * from books order by id desc''')
        current_book_data = c.fetchone()
        dbc.close()
        msg = 'Not Found'
        return render_template('index_abby2.html', msg=msg, bookData=book_data, getPassword=False, currentBook=current_book_data)


@app.route('/CheckOutBookAbby', methods=['POST'])
def checkOutBookAbby():
    current_book_id = request.form['checkOut']
    borrower = request.form['bName']
    dbc = sqlite3.connect('library_abby.db')
    c = dbc.cursor()
    c.execute('''update books SET available=? where id=?''',('N',str(current_book_id)))
    dbc.commit()
    c.execute('''update books SET borrower=? where id=?''',(borrower,str(current_book_id)))
    dbc.commit()
    dbc.close()
    return redirect('/Abby', code=302)

@app.route('/CheckInAbby', methods=['POST'])
def checkInBookAbby():
    current_book_id = request.form['checkIn']
    dbc = sqlite3.connect('library_abby.db')
    c = dbc.cursor()
    c.execute('''update books SET available=? where id=?''',('Y',str(current_book_id)))
    dbc.commit()
    c.execute('''update books SET borrower=? where id=?''',('',str(current_book_id)))
    dbc.commit()
    dbc.close()
    return redirect('/Abby', code=302)


@app.route('/RemoveBookAbby', methods=['POST'])
def removebookAbby():
    if request.form['password'] == os.environ['DELETE_PASSWORD_ABBY']:
        dbc = sqlite3.connect('library_abby.db')
        c = dbc.cursor()
        book_id = request.form['bID']
        c.execute('delete from books where id=' + str(book_id) + ';')
        dbc.commit()
        dbc.close()
    return redirect("/Abby", code=302)



##########################################################################

@app.route('/OldHome')
def OldHome():
    KEY = os.environ['KEY']
    db = sqlite3.connect('testdb.db')
    c = db.cursor()
    c.execute(''' insert into user (name) values ('david')''')
    db.commit()
    c.execute('''select * from user''')

    data = c.fetchall()

    db.close()

    return render_template('new_home.html', key=KEY, data=data)

@app.route('/lexi')
def Lexi():
    return render_template('lexi.com.html',User='David')

@app.route('/AllAboutMe')
def aam():
    return render_template('AllAboutMe.html',words1='This is a story about Lexi', words2='she loves to play all day')


