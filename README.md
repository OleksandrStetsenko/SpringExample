<div class="content html_format">
            <i>Не так давно я начал изучать фреймворк Spring и понял, что количество материала на русском языке ограниченно буквально парой стоящих статей. По быстрому пробежав, я захотел сделать что-то более интересное, но с наскоку взять не удалось. Пришлось погуглить по поводу нескольких вопросов касательно взаимодействия Spring и Hibernate. Неожиданно я наткнулся на <a href="http://krams915.blogspot.ru">блог</a> достаточно интересного разработчика Mark Serrano aka krams. Теперь вместе с вами я хотел бы начать цикл статей-переводов, а так же свое обучение в мире Spring.</i><br>
<a href="http://habrahabr.ru/post/249073/"> Spring — Hibernate: ассоциация один ко многим </a><br>
Приступим… <br>
<a name="habracut"></a><br>
В этом уроке мы создадим простое приложение для управления списком лиц с использованием Spring MVC 3. Мы построим простую CRUD (Create Read Update Delete) систему для просмотра, добавления, правки и удаления персон. В качестве слоя для работы с базой данных мы будем использовать Hibernate 3 и базу MySQL, хотя вы можете использовать другую базу данных. В этом уроке предполагается, что вы знакомы с MVC, ORM и SQL (<i>от переводчика</i>: не пугайтесь этих сложных слов, смело читаем, все будет довольно просто)<br>
<br>
<h4>Что такое Hibernate?</h4><br>
<b>Hibernate</b> — библиотека для языка программирования Java, предназначенная для решения задач объектно-реляционного отображения (object-relational mapping — ORM). Данная библиотека предоставляет лёгкий в использовании каркас (фреймворк) для отображения объектно-ориентированной модели данных в традиционные реляционные базы данных.<br>
<br>
Hibernate решает проблему между хранением объекта в базе данных и его объектно-ориентированном представлении на уровне языка. <br>
<br>
<h4>Что такое MySQL?</h4><br>
База данных MySQL обеспечивает работу наиболее требовательных сетей, электронной комерции и обработки транзакций приложений. Она гарантирует безопасность транзакций, ACID совместимость дает возможность комита, отката, восстановления в случае сбоя, а также возможностей блокировки строк. MySQL обеспечивает простоту использования, масштабируемость и производительность, эти качества сделали MySQL наиболее популярной базой данных с открытым исходным кодом в мире. Некоторые из наиболее посещаемых ресурсов сети использует MySQL, такие как Facebook, Google, Ticketmaster и Ebay.<br>
<br>
В начале давайте посмотрим на конечную структуру нашего проекта:<br>
<br>
<img src="http://habrastorage.org/getpro/habr/post_images/060/b3a/cf5/060b3acf53333f796e9541b374009b33.png" alt="image" width="173" height="400"><br>
<br>
И посмотрим, как будет выглядеть наше приложение:<br>
<br>
<img src="http://habrastorage.org/getpro/habr/post_images/123/3d4/b8e/1233d4b8e791babdc5e839ff06550ea2.png" alt="image" width="400" height="130"><br>
<br>
Мы начнем с определения нашего доменного объекта Person (сущности Person).<br>
<br>
<b>Person</b><br>
<br>
<pre><code class="java"><span class="keyword">package</span> org.krams.tutorial.home.stetsenko.domain;
 
<span class="keyword">import</span> java.io.Serializable;
 
<span class="keyword">import</span> javax.persistence.Column;
<span class="keyword">import</span> javax.persistence.Entity;
<span class="keyword">import</span> javax.persistence.GeneratedValue;
<span class="keyword">import</span> javax.persistence.Id;
<span class="keyword">import</span> javax.persistence.Table;
 
<span class="javadoc">/**
 * For a complete reference see
 * &lt;a href="http://docs.jboss.org/hibernate/stable/annotations/reference/en/html_single/"&gt;
 * Hibernate Annotations Communit Documentations&lt;/a&gt;
 */</span>
<span class="annotation">@Entity</span>
<span class="annotation">@Table</span>(name = <span class="string">"PERSON"</span>)
<span class="keyword">public</span> <span class="class"><span class="keyword">class</span> <span class="title">Person</span> <span class="keyword">implements</span> <span class="title">Serializable</span> {</span>
 
 <span class="keyword">private</span> <span class="keyword">static</span> <span class="keyword">final</span> <span class="keyword">long</span> serialVersionUID = -<span class="number">5527566248002296042</span>L;
  
 <span class="annotation">@Id</span>
 <span class="annotation">@Column</span>(name = <span class="string">"ID"</span>)
 <span class="annotation">@GeneratedValue</span>
 <span class="keyword">private</span> Integer id;
  
 <span class="annotation">@Column</span>(name = <span class="string">"FIRST_NAME"</span>)
 <span class="keyword">private</span> String firstName;
  
 <span class="annotation">@Column</span>(name = <span class="string">"LAST_NAME"</span>)
 <span class="keyword">private</span> String lastName;
  
 <span class="annotation">@Column</span>(name = <span class="string">"MONEY"</span>)
 <span class="keyword">private</span> Double money;
 
 <span class="keyword">public</span> Integer getId() {
  <span class="keyword">return</span> id;
 }
 
 <span class="keyword">public</span> <span class="keyword">void</span> setId(Integer id) {
  <span class="keyword">this</span>.id = id;
 }
 
 <span class="keyword">public</span> String getFirstName() {
  <span class="keyword">return</span> firstName;
 }
 
 <span class="keyword">public</span> <span class="keyword">void</span> setFirstName(String firstName) {
  <span class="keyword">this</span>.firstName = firstName;
 }
 
 <span class="keyword">public</span> String getLastName() {
  <span class="keyword">return</span> lastName;
 }
 
 <span class="keyword">public</span> <span class="keyword">void</span> setLastName(String lastName) {
  <span class="keyword">this</span>.lastName = lastName;
 }
 
 <span class="keyword">public</span> Double getMoney() {
  <span class="keyword">return</span> money;
 }
 
 <span class="keyword">public</span> <span class="keyword">void</span> setMoney(Double money) {
  <span class="keyword">this</span>.money = money;
 }
}
</code></pre><br>
Person простой POJO содержащий четыре приватных переменных:<br>
<br>
id<br>
firstName<br>
lastName<br>
money<br>
<br>
<i>Дополнение от переводчика</i>: POJO (англ.&nbsp;Plain Old Java Object) — «простой Java-объект в старом стиле», простой Java-объект, не унаследованный от какого-то специфического объекта и не реализующий никаких служебных интерфейсов сверх тех, которые нужны для бизнес-модели (Wiki)<br>
<br>
Каждой из этих переменных с аннотацией @Column соответствует колонка в базе данных:<br>
<br>
ID<br>
FIRST_NAME<br>
LAST_NAME<br>
MONEY<br>
<br>
Вам нет необходимости иметь с ними дело, Hibernate возьмет это на себя. Тем не менее вы должны правильно объявить эти имена (например @Column(name = ``ID``)). Вы не объявляете их в базе данных. Помните — вашей базы данных еще не существует.<br>
<br>
POJO ссылается на таблицу вашей базы данных, обратите внимание на аннотацию <a href="http://habrahabr.ru/users/table/" class="user_link">Table</a>, где указано имя таблицы соответствующей данному объекту.<br>
<br>
<pre><code class="java"><span class="annotation">@Entity</span>
<span class="annotation">@Table</span>(name = <span class="string">"PERSON"</span>)
<span class="keyword">public</span> <span class="class"><span class="keyword">class</span> <span class="title">Person</span> <span class="keyword">implements</span> <span class="title">Serializable</span>
</span></code></pre><br>
Обратите внимание, аннотация <a href="http://habrahabr.ru/users/entity/" class="user_link">Entity</a> стоит перед аннотацие <a href="http://habrahabr.ru/users/table/" class="user_link">Table</a>, это говорит Hibernate, что POJO является отображением таблицы базы данных.<br>
<br>
Мы будем манипулирувать списком лиц, поэтому создадим для этого сервис:<br>
<br>
<b>PersonService</b><br>
<br>
<pre><code class="java"><span class="keyword">package</span> org.krams.tutorial.home.stetsenko.service;
 
<span class="keyword">import</span> java.util.List;
 
<span class="keyword">import</span> javax.annotation.Resource;
 
<span class="keyword">import</span> org.apache.log4j.Logger;
<span class="keyword">import</span> org.hibernate.Query;
<span class="keyword">import</span> org.hibernate.Session;
<span class="keyword">import</span> org.hibernate.SessionFactory;
<span class="keyword">import</span> org.krams.tutorial.home.stetsenko.domain.Person;
<span class="keyword">import</span> org.springframework.stereotype.Service;
<span class="keyword">import</span> org.springframework.transaction.annotation.Transactional;
 
<span class="javadoc">/**
 * Service for processing Persons
 *
 */</span>
<span class="annotation">@Service</span>(<span class="string">"personService"</span>)
<span class="annotation">@Transactional</span>
<span class="keyword">public</span> <span class="class"><span class="keyword">class</span> <span class="title">PersonService</span> {</span>
 
 <span class="keyword">protected</span> <span class="keyword">static</span> Logger logger = Logger.getLogger(<span class="string">"home.stetsenko.service"</span>);
  
 <span class="annotation">@Resource</span>(name=<span class="string">"sessionFactory"</span>)
 <span class="keyword">private</span> SessionFactory sessionFactory;
  
 <span class="javadoc">/**
  * Retrieves all persons
  *
  * <span class="javadoctag">@return</span> a list of persons
  */</span>
 <span class="keyword">public</span> List&lt;person&gt; getAll() {
  logger.debug(<span class="string">"Retrieving all persons"</span>);
   
  <span class="comment">// Retrieve session from Hibernate</span>
  Session session = sessionFactory.getCurrentSession();
   
  <span class="comment">// Create a Hibernate query (HQL)</span>
  Query query = session.createQuery(<span class="string">"FROM  Person"</span>);
   
  <span class="comment">// Retrieve all</span>
  <span class="keyword">return</span>  query.list();
 }
  
 <span class="javadoc">/**
  * Retrieves a single person
  */</span>
 <span class="keyword">public</span> Person get( Integer id ) {
  <span class="comment">// Retrieve session from Hibernate</span>
  Session session = sessionFactory.getCurrentSession();
   
  <span class="comment">// Retrieve existing person first</span>
  Person person = (Person) session.get(Person.class, id);
   
  <span class="keyword">return</span> person;
 }
 <span class="javadoc">/**
  * Adds a new person
  */</span>
 <span class="keyword">public</span> <span class="keyword">void</span> add(Person person) {
  logger.debug(<span class="string">"Adding new person"</span>);
   
  <span class="comment">// Retrieve session from Hibernate</span>
  Session session = sessionFactory.getCurrentSession();
   
  <span class="comment">// Save</span>
  session.save(person);
 }
  
 <span class="javadoc">/**
  * Deletes an existing person
  * <span class="javadoctag">@param</span> id the id of the existing person
  */</span>
 <span class="keyword">public</span> <span class="keyword">void</span> delete(Integer id) {
  logger.debug(<span class="string">"Deleting existing person"</span>);
   
  <span class="comment">// Retrieve session from Hibernate</span>
  Session session = sessionFactory.getCurrentSession();
   
  <span class="comment">// Retrieve existing person first</span>
  Person person = (Person) session.get(Person.class, id);
   
  <span class="comment">// Delete</span>
  session.delete(person);
 }
  
 <span class="javadoc">/**
  * Edits an existing person
  */</span>
 <span class="keyword">public</span> <span class="keyword">void</span> edit(Person person) {
  logger.debug(<span class="string">"Editing existing person"</span>);
   
  <span class="comment">// Retrieve session from Hibernate</span>
  Session session = sessionFactory.getCurrentSession();
   
  <span class="comment">// Retrieve existing person via id</span>
  Person existingPerson = (Person) session.get(Person.class, person.getId());
   
  <span class="comment">// Assign updated values to this person</span>
  existingPerson.setFirstName(person.getFirstName());
  existingPerson.setLastName(existingPerson.getLastName());
  existingPerson.setMoney(existingPerson.getMoney());
 
  <span class="comment">// Save updates</span>
  session.save(existingPerson);
 }
}
</code></pre><br>
<br>
Мы объявили простую CRUD систему со следующими методами:<br>
<br>
getAll()<br>
add()<br>
delete()<br>
edit()<br>
<br>
В каждом методе мы получаем сессию:<br>
<br>
Session session = sessionFactory.getCurrentSession();<br>
<br>
Это похоже на соединение с базой данных, так что мы можем делать нашу работу. Объект Session предоставляет множество методов для работы объектами-сущностями. Для этого урока мы используем следующие методы класса Session:<br>
<br>
session.createQuery()<br>
session.save()<br>
session.delete()<br>
<br>
Мы создали доменный и сервисный слои, давайте создадим Spring контроллер.<br>
<br>
<b>MainController</b><br>
<pre><code class="java"><span class="keyword">package</span> org.krams.tutorial.home.stetsenko.controller;
 
<span class="keyword">import</span> java.util.List;
 
<span class="keyword">import</span> javax.annotation.Resource;
 
<span class="keyword">import</span> org.apache.log4j.Logger;
<span class="keyword">import</span> org.krams.tutorial.home.stetsenko.domain.Person;
<span class="keyword">import</span> org.krams.tutorial.home.stetsenko.service.PersonService;
<span class="keyword">import</span> org.springframework.stereotype.Controller;
<span class="keyword">import</span> org.springframework.ui.Model;
<span class="keyword">import</span> org.springframework.web.bind.annotation.ModelAttribute;
<span class="keyword">import</span> org.springframework.web.bind.annotation.RequestMapping;
<span class="keyword">import</span> org.springframework.web.bind.annotation.RequestMethod;
<span class="keyword">import</span> org.springframework.web.bind.annotation.RequestParam;
 
 
<span class="javadoc">/**
 * Handles and retrieves person request
 */</span>
<span class="annotation">@Controller</span>
<span class="annotation">@RequestMapping</span>(<span class="string">"/main"</span>)
<span class="keyword">public</span> <span class="class"><span class="keyword">class</span> <span class="title">MainController</span> {</span>
 
 <span class="keyword">protected</span> <span class="keyword">static</span> Logger logger = Logger.getLogger(<span class="string">"home.stetsenko.controller"</span>);
  
 <span class="annotation">@Resource</span>(name=<span class="string">"personService"</span>)
 <span class="keyword">private</span> PersonService personService;
  
 <span class="javadoc">/**
  * Handles and retrieves all persons and show it in a JSP page
  *
  * <span class="javadoctag">@return</span> the name of the JSP page
  */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons"</span>, method = RequestMethod.GET)
    <span class="keyword">public</span> String getPersons(Model model) {
      
     logger.debug(<span class="string">"Received request to show all persons"</span>);
      
     <span class="comment">// Retrieve all persons by delegating the call to PersonService</span>
     List&lt;person&gt; persons = personService.getAll();
      
     <span class="comment">// Attach persons to the Model</span>
     model.addAttribute(<span class="string">"persons"</span>, persons);
      
     <span class="comment">// This will resolve to /WEB-INF/jsp/personspage.jsp</span>
     <span class="keyword">return</span> <span class="string">"personspage"</span>;
 }
     
    <span class="javadoc">/**
     * Retrieves the add page
     *
     * <span class="javadoctag">@return</span> the name of the JSP page
     */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons/add"</span>, method = RequestMethod.GET)
    <span class="keyword">public</span> String getAdd(Model model) {
     logger.debug(<span class="string">"Received request to show add page"</span>);
     
     <span class="comment">// Create new Person and add to model</span>
     <span class="comment">// This is the formBackingOBject</span>
     model.addAttribute(<span class="string">"personAttribute"</span>, <span class="keyword">new</span> Person());
 
     <span class="comment">// This will resolve to /WEB-INF/jsp/addpage.jsp</span>
     <span class="keyword">return</span> <span class="string">"addpage"</span>;
 }
  
    <span class="javadoc">/**
     * Adds a new person by delegating the processing to PersonService.
     * Displays a confirmation JSP page
     *
     * <span class="javadoctag">@return</span>  the name of the JSP page
     */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons/add"</span>, method = RequestMethod.POST)
    <span class="keyword">public</span> String add(<span class="annotation">@ModelAttribute</span>(<span class="string">"personAttribute"</span>) Person person) {
  logger.debug(<span class="string">"Received request to add new person"</span>);
   
     <span class="comment">// The "personAttribute" model has been passed to the home.stetsenko.controller from the JSP</span>
     <span class="comment">// We use the name "personAttribute" because the JSP uses that name</span>
   
  <span class="comment">// Call PersonService to do the actual adding</span>
  personService.add(person);
 
     <span class="comment">// This will resolve to /WEB-INF/jsp/addedpage.jsp</span>
  <span class="keyword">return</span> <span class="string">"addedpage"</span>;
 }
     
    <span class="javadoc">/**
     * Deletes an existing person by delegating the processing to PersonService.
     * Displays a confirmation JSP page
     *
     * <span class="javadoctag">@return</span>  the name of the JSP page
     */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons/delete"</span>, method = RequestMethod.GET)
    <span class="keyword">public</span> String delete(<span class="annotation">@RequestParam</span>(value=<span class="string">"id"</span>, required=<span class="keyword">true</span>) Integer id,
              Model model) {
    
  logger.debug(<span class="string">"Received request to delete existing person"</span>);
   
  <span class="comment">// Call PersonService to do the actual deleting</span>
  personService.delete(id);
   
  <span class="comment">// Add id reference to Model</span>
  model.addAttribute(<span class="string">"id"</span>, id);
      
     <span class="comment">// This will resolve to /WEB-INF/jsp/deletedpage.jsp</span>
  <span class="keyword">return</span> <span class="string">"deletedpage"</span>;
 }
     
    <span class="javadoc">/**
     * Retrieves the edit page
     *
     * <span class="javadoctag">@return</span> the name of the JSP page
     */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons/edit"</span>, method = RequestMethod.GET)
    <span class="keyword">public</span> String getEdit(<span class="annotation">@RequestParam</span>(value=<span class="string">"id"</span>, required=<span class="keyword">true</span>) Integer id, 
              Model model) {
     logger.debug(<span class="string">"Received request to show edit page"</span>);
     
     <span class="comment">// Retrieve existing Person and add to model</span>
     <span class="comment">// This is the formBackingOBject</span>
     model.addAttribute(<span class="string">"personAttribute"</span>, personService.get(id));
      
     <span class="comment">// This will resolve to /WEB-INF/jsp/editpage.jsp</span>
     <span class="keyword">return</span> <span class="string">"editpage"</span>;
 }
     
    <span class="javadoc">/**
     * Edits an existing person by delegating the processing to PersonService.
     * Displays a confirmation JSP page
     *
     * <span class="javadoctag">@return</span>  the name of the JSP page
     */</span>
    <span class="annotation">@RequestMapping</span>(value = <span class="string">"/persons/edit"</span>, method = RequestMethod.POST)
    <span class="keyword">public</span> String saveEdit(<span class="annotation">@ModelAttribute</span>(<span class="string">"personAttribute"</span>) Person person,
                 <span class="annotation">@RequestParam</span>(value=<span class="string">"id"</span>, required=<span class="keyword">true</span>) Integer id,
                Model model) {
     logger.debug(<span class="string">"Received request to update person"</span>);
     
     <span class="comment">// The "personAttribute" model has been passed to the home.stetsenko.controller from the JSP</span>
     <span class="comment">// We use the name "personAttribute" because the JSP uses that name</span>
      
     <span class="comment">// We manually assign the id because we disabled it in the JSP page</span>
     <span class="comment">// When a field is disabled it will not be included in the ModelAttribute</span>
     person.setId(id);
      
     <span class="comment">// Delegate to PersonService for editing</span>
     personService.edit(person);
      
     <span class="comment">// Add id reference to Model</span>
  model.addAttribute(<span class="string">"id"</span>, id);
   
     <span class="comment">// This will resolve to /WEB-INF/jsp/editedpage.jsp</span>
  <span class="keyword">return</span> <span class="string">"editedpage"</span>;
 }
     
}
</code></pre><br>
Контроллер объявляет следующие представления:<br>
<br>
/persons — для получения всех лиц<br>
/persons/add (GET) — показать форму «Добавить»<br>
/persons/add (POST) — сохраняет новую персону<br>
/persons/delete — удаляет существующую персону<br>
/persons/edit (GET) — показывает форму «Правка»<br>
/persons/edit (POST) — сохраняет «исправленную» персону<br>
<br>
Каждое представление вызывает PersonService. Когда PersonService завершает обработку, крнтроллер пересылает запрос на JSP-страницу, которая показывает сообщение с подтверждением. Вот страницы JSP:<br>
<br>
<b>personspage.jsp</b><br>
<br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Persons<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"addUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons/add"</span> /&gt;</span>
<span class="tag">&lt;<span class="title">table</span> <span class="attribute">style</span>=<span class="value">"border: </span><span class="number">1</span><span class="xml">px solid; width: </span><span class="number">500</span><span class="xml">px; text-align:center"&gt;
 <span class="tag">&lt;<span class="title">thead</span> <span class="attribute">style</span>=<span class="value">"background:#fcf"</span>&gt;</span>
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">th</span>&gt;</span>First Name<span class="tag">&lt;/<span class="title">th</span>&gt;</span>
   <span class="tag">&lt;<span class="title">th</span>&gt;</span>Last Name<span class="tag">&lt;/<span class="title">th</span>&gt;</span>
   <span class="tag">&lt;<span class="title">th</span>&gt;</span>Money<span class="tag">&lt;/<span class="title">th</span>&gt;</span>
   <span class="tag">&lt;<span class="title">th</span> <span class="attribute">colspan</span>=<span class="value">"</span><span class="number">3</span><span class="xml">"&gt;<span class="tag">&lt;/<span class="title">th</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">thead</span>&gt;</span>
 <span class="tag">&lt;<span class="title">tbody</span>&gt;</span>
 <span class="tag">&lt;<span class="title">c:forEach</span> <span class="attribute">items</span>=<span class="value">"</span><span class="variable">${persons}</span><span class="xml">" var="person"&gt;
   <span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"editUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons/edit?id=</span><span class="variable">${person.id}</span><span class="xml">" /&gt;
   <span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"deleteUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons/delete?id=</span><span class="variable">${person.id}</span><span class="xml">" /&gt;
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">c:out</span> <span class="attribute">value</span>=<span class="value">"</span><span class="variable">${person.firstName}</span><span class="xml">" /&gt;<span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">c:out</span> <span class="attribute">value</span>=<span class="value">"</span><span class="variable">${person.lastName}</span><span class="xml">" /&gt;<span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">c:out</span> <span class="attribute">value</span>=<span class="value">"</span><span class="variable">${person.money}</span><span class="xml">" /&gt;<span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${editUrl}</span><span class="xml">"&gt;Edit<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${deleteUrl}</span><span class="xml">"&gt;Delete<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${addUrl}</span><span class="xml">"&gt;Add<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">c:forEach</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">tbody</span>&gt;</span>
<span class="tag">&lt;/<span class="title">table</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">c:if</span> <span class="attribute">test</span>=<span class="value">"</span><span class="variable">${empty</span><span class="xml"> persons}"&gt;
 There are currently no persons in the list. <span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${addUrl}</span><span class="xml">"&gt;Add<span class="tag">&lt;/<span class="title">a</span>&gt;</span> a person.
<span class="tag">&lt;/<span class="title">c:if</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></pre><br>
<br>
<b>editpage.jsp</b><br>
<br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://www.springframework.org/tags/form"</span> prefix=<span class="string">"form"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Edit Person<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"saveUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons/edit?id=</span><span class="variable">${personAttribute.id}</span><span class="xml">" /&gt;
<span class="tag">&lt;<span class="title">form:form</span> <span class="attribute">modelAttribute</span>=<span class="value">"personAttribute"</span> <span class="attribute">method</span>=<span class="value">"POST"</span> <span class="attribute">action</span>=<span class="value">"</span><span class="variable">${saveUrl}</span><span class="xml">"&gt;
 <span class="tag">&lt;<span class="title">table</span>&gt;</span>
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"id"</span>&gt;</span>Id:<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"id"</span> <span class="attribute">disabled</span>=<span class="value">"true"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
  
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"firstName"</span>&gt;</span>First Name:<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"firstName"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"lastName"</span>&gt;</span>Last Name<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"lastName"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
   
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"money"</span>&gt;</span>Money<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"money"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">table</span>&gt;</span>
  
 <span class="tag">&lt;<span class="title">input</span> <span class="attribute">type</span>=<span class="value">"submit"</span> <span class="attribute">value</span>=<span class="value">"Save"</span> /&gt;</span>
<span class="tag">&lt;/<span class="title">form:form</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></span></span></code></pre><br>
<br>
<b>addpage.jsp</b><br>
<br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://www.springframework.org/tags/form"</span> prefix=<span class="string">"form"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Create New Person<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"saveUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons/add"</span> /&gt;</span>
<span class="tag">&lt;<span class="title">form:form</span> <span class="attribute">modelAttribute</span>=<span class="value">"personAttribute"</span> <span class="attribute">method</span>=<span class="value">"POST"</span> <span class="attribute">action</span>=<span class="value">"</span><span class="variable">${saveUrl}</span><span class="xml">"&gt;
 <span class="tag">&lt;<span class="title">table</span>&gt;</span>
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"firstName"</span>&gt;</span>First Name:<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"firstName"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"lastName"</span>&gt;</span>Last Name<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"lastName"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
   
  <span class="tag">&lt;<span class="title">tr</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:label</span> <span class="attribute">path</span>=<span class="value">"money"</span>&gt;</span>Money<span class="tag">&lt;/<span class="title">form:label</span>&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
   <span class="tag">&lt;<span class="title">td</span>&gt;</span><span class="tag">&lt;<span class="title">form:input</span> <span class="attribute">path</span>=<span class="value">"money"</span>/&gt;</span><span class="tag">&lt;/<span class="title">td</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">tr</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">table</span>&gt;</span>
  
 <span class="tag">&lt;<span class="title">input</span> <span class="attribute">type</span>=<span class="value">"submit"</span> <span class="attribute">value</span>=<span class="value">"Save"</span> /&gt;</span>
<span class="tag">&lt;/<span class="title">form:form</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></code></pre><br>
<br>
<b>editedpage.jsp</b><br>
<br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page import=<span class="string">"java.util.Date"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Persons<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>You have edited a person with id </span><span class="variable">${id}</span><span class="xml"> at <span class="vbscript">&lt;%= <span class="keyword">new</span> java.util.<span class="built_in">Date</span>() %&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"mainUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons"</span> /&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>Return to <span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${mainUrl}</span><span class="xml">"&gt;Main List<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></code></pre><br>
<b>addedpage.jsp</b><br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page import=<span class="string">"java.util.Date"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Persons<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>You have added a new person at <span class="vbscript">&lt;%= <span class="keyword">new</span> java.util.<span class="built_in">Date</span>() %&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"mainUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons"</span> /&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>Return to <span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${mainUrl}</span><span class="xml">"&gt;Main List<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></code></pre><br>
<b>deletedpage.jsp</b><br>
<pre><code class="html parser3"><span class="xml"><span class="vbscript">&lt;%@ taglib uri=<span class="string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="string">"c"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page import=<span class="string">"java.util.Date"</span> %&gt;</span>
<span class="vbscript">&lt;%@ page language=<span class="string">"java"</span> contentType=<span class="string">"text/html; charset=UTF-</span></span><span class="number">8</span><span class="xml">"
    pageEncoding="UTF-</span><span class="number">8</span><span class="xml">"%&gt;
<span class="doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML </span><span class="number">4.01</span><span class="xml"> Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
<span class="tag">&lt;<span class="title">html</span>&gt;</span>
<span class="tag">&lt;<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">meta</span> <span class="attribute">http-equiv</span>=<span class="value">"Content-Type"</span> <span class="attribute">content</span>=<span class="value">"text/html; charset=UTF-</span><span class="number">8</span><span class="xml">"&gt;
<span class="tag">&lt;<span class="title">title</span>&gt;</span>Insert title here<span class="tag">&lt;/<span class="title">title</span>&gt;</span>
<span class="tag">&lt;/<span class="title">head</span>&gt;</span>
<span class="tag">&lt;<span class="title">body</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">h1</span>&gt;</span>Persons<span class="tag">&lt;/<span class="title">h1</span>&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>You have deleted a person with id </span><span class="variable">${id}</span><span class="xml"> at <span class="vbscript">&lt;%= <span class="keyword">new</span> java.util.<span class="built_in">Date</span>() %&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;<span class="title">c:url</span> <span class="attribute">var</span>=<span class="value">"mainUrl"</span> <span class="attribute">value</span>=<span class="value">"/krams/main/persons"</span> /&gt;</span>
<span class="tag">&lt;<span class="title">p</span>&gt;</span>Return to <span class="tag">&lt;<span class="title">a</span> <span class="attribute">href</span>=<span class="value">"</span><span class="variable">${mainUrl}</span><span class="xml">"&gt;Main List<span class="tag">&lt;/<span class="title">a</span>&gt;</span><span class="tag">&lt;/<span class="title">p</span>&gt;</span>
 
<span class="tag">&lt;/<span class="title">body</span>&gt;</span>
<span class="tag">&lt;/<span class="title">html</span>&gt;</span>
</span></span></span></span></span></span></span></code></pre><br>
Давайте сконфигурируем наше приложение.<br>
Для работы Spring MVC в web.xml добавим:<br>
<br>
<b>web.xml</b><br>
<br>
<pre><code class="xml"><span class="tag">&lt;<span class="title">servlet</span>&gt;</span>
  <span class="tag">&lt;<span class="title">servlet-name</span>&gt;</span>spring<span class="tag">&lt;/<span class="title">servlet-name</span>&gt;</span>
  <span class="tag">&lt;<span class="title">servlet-class</span>&gt;</span>org.springframework.web.servlet.DispatcherServlet<span class="tag">&lt;/<span class="title">servlet-class</span>&gt;</span>
  <span class="tag">&lt;<span class="title">load-on-startup</span>&gt;</span>1<span class="tag">&lt;/<span class="title">load-on-startup</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">servlet</span>&gt;</span>
  
 <span class="tag">&lt;<span class="title">servlet-mapping</span>&gt;</span>
  <span class="tag">&lt;<span class="title">servlet-name</span>&gt;</span>spring<span class="tag">&lt;/<span class="title">servlet-name</span>&gt;</span>
  <span class="tag">&lt;<span class="title">url-pattern</span>&gt;</span>/krams/*<span class="tag">&lt;/<span class="title">url-pattern</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">servlet-mapping</span>&gt;</span>
 
 <span class="tag">&lt;<span class="title">listener</span>&gt;</span>
  <span class="tag">&lt;<span class="title">listener-class</span>&gt;</span>org.springframework.web.context.ContextLoaderListener<span class="tag">&lt;/<span class="title">listener-class</span>&gt;</span>
 <span class="tag">&lt;/<span class="title">listener</span>&gt;</span>
</code></pre><br>
Обратите внимание на шаблон URL. При обращении к страницам нашего приложения к имени хоста должна быть добавка в виде:<br>
<br>
/krams<br>
<br>
В web.xml мы указали в качестве имени сервлета spring. По соглашению мы также должны создать файл spring-servlet.xml.<br>
<br>
<b>spring-servlet.xml</b><br>
<br>
<pre><code class="xml"><span class="comment">&lt;!-- Declare a view resolver --&gt;</span>
 <span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"viewResolver"</span> <span class="attribute">class</span>=<span class="value">"org.springframework.web.servlet.view.InternalResourceViewResolver"</span>
      <span class="attribute">p:prefix</span>=<span class="value">"/WEB-INF/jsp/"</span> <span class="attribute">p:suffix</span>=<span class="value">".jsp"</span> /&gt;</span>
</code></pre><br>
Мы также должны создать файл applicationContext.xml.<br>
<br>
<b>applicationContext.xml</b><br>
<br>
<pre><code class="xml"><span class="comment">&lt;!-- Activates various annotations to be detected in bean classes --&gt;</span>
 <span class="tag">&lt;<span class="title">context:annotation-config</span> /&gt;</span>
  
 <span class="comment">&lt;!-- Scans the classpath for annotated components that will be auto-registered as Spring beans.
  For example @Controller and @Service. Make sure to set the correct base-package--&gt;</span>
 <span class="tag">&lt;<span class="title">context:component-scan</span> <span class="attribute">base-package</span>=<span class="value">"org.krams.tutorial"</span> /&gt;</span>
  
 <span class="comment">&lt;!-- Configures the annotation-driven Spring MVC Controller programming model.
 Note that, with Spring 3.0, this tag works in Servlet MVC only!  --&gt;</span>
 <span class="tag">&lt;<span class="title">mvc:annotation-driven</span> /&gt;</span>
  
 <span class="comment">&lt;!-- Load Hibernate related configuration --&gt;</span>
 <span class="tag">&lt;<span class="title">import</span> <span class="attribute">resource</span>=<span class="value">"hibernate-context.xml"</span> /&gt;</span>
</code></pre><br>
Обратите внимание, что в файле applicationContext.xml мы объявили следующий импорт:<br>
<br>
<pre><code class="java">&lt;<span class="keyword">import</span> resource=<span class="string">"hibernate-context.xml"</span> /&gt;
</code></pre><br>
Он содержит файлы конфигурации Hibernate.<br>
<br>
<b>Hibernate-context.xml</b><br>
<br>
<pre><code class="xml"><span class="pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="tag">&lt;<span class="title">beans</span>  <span class="attribute">xmlns</span>=<span class="value">"http://www.springframework.org/schema/beans"</span>
        <span class="attribute">xmlns:xsi</span>=<span class="value">"http://www.w3.org/2001/XMLSchema-instance"</span>
        <span class="attribute">xmlns:p</span>=<span class="value">"http://www.springframework.org/schema/p"</span>
        <span class="attribute">xmlns:tx</span>=<span class="value">"http://www.springframework.org/schema/tx"</span>
        <span class="attribute">xmlns:context</span>=<span class="value">"http://www.springframework.org/schema/context"</span>
        <span class="attribute">xsi:schemaLocation</span>=<span class="value">"
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context-3.0.xsd
      "</span>&gt;</span>
 
 <span class="tag">&lt;<span class="title">context:property-placeholder</span> <span class="attribute">location</span>=<span class="value">"/WEB-INF/spring.properties"</span> /&gt;</span>
     
    <span class="comment">&lt;!-- Enable annotation style of managing transactions --&gt;</span>
 <span class="tag">&lt;<span class="title">tx:annotation-driven</span> <span class="attribute">transaction-manager</span>=<span class="value">"transactionManager"</span> /&gt;</span>
    
    <span class="comment">&lt;!-- Declare the Hibernate SessionFactory for retrieving Hibernate sessions --&gt;</span>
    <span class="comment">&lt;!-- See http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/orm/hibernate3/annotation/AnnotationSessionFactoryBean.html --&gt;</span>       
 <span class="comment">&lt;!-- See http://docs.jboss.org/hibernate/stable/core/api/index.html?org/hibernate/SessionFactory.html --&gt;</span>
 <span class="comment">&lt;!-- See http://docs.jboss.org/hibernate/stable/core/api/index.html?org/hibernate/Session.html --&gt;</span>
 <span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"sessionFactory"</span> <span class="attribute">class</span>=<span class="value">"org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean"</span>
     <span class="attribute">p:dataSource-ref</span>=<span class="value">"dataSource"</span>
     <span class="attribute">p:configLocation</span>=<span class="value">"${hibernate.config}"</span>
     <span class="attribute">p:packagesToScan</span>=<span class="value">"org.krams.tutorial"</span>/&gt;</span>
  
 <span class="comment">&lt;!-- Declare a datasource that has pooling capabilities--&gt;</span> 
 <span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"dataSource"</span> <span class="attribute">class</span>=<span class="value">"com.mchange.v2.c3p0.ComboPooledDataSource"</span>
    <span class="attribute">destroy-method</span>=<span class="value">"close"</span>
    <span class="attribute">p:driverClass</span>=<span class="value">"${app.jdbc.driverClassName}"</span>
    <span class="attribute">p:jdbcUrl</span>=<span class="value">"${app.jdbc.url}"</span>
    <span class="attribute">p:user</span>=<span class="value">"${app.jdbc.username}"</span>
    <span class="attribute">p:password</span>=<span class="value">"${app.jdbc.password}"</span>
    <span class="attribute">p:acquireIncrement</span>=<span class="value">"5"</span>
    <span class="attribute">p:idleConnectionTestPeriod</span>=<span class="value">"60"</span>
    <span class="attribute">p:maxPoolSize</span>=<span class="value">"100"</span>
    <span class="attribute">p:maxStatements</span>=<span class="value">"50"</span>
    <span class="attribute">p:minPoolSize</span>=<span class="value">"10"</span> /&gt;</span>
 
 <span class="comment">&lt;!-- Declare a transaction manager--&gt;</span>
 <span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"transactionManager"</span> <span class="attribute">class</span>=<span class="value">"org.springframework.orm.hibernate3.HibernateTransactionManager"</span>
          <span class="attribute">p:sessionFactory-ref</span>=<span class="value">"sessionFactory"</span> /&gt;</span>
   
<span class="tag">&lt;/<span class="title">beans</span>&gt;</span>
</code></pre><br>
<br>
В этом файле мы инкапсулировали все связанные конфигурации Hibernate и Spring. <br>
Комментарии по конфигурации:<br>
<br>
1. Включаем поддержку транзакций через использование аннотаций Spring.<br>
<br>
<pre><code class="xml"><span class="tag">&lt;<span class="title">tx:annotation-driven</span> <span class="attribute">transaction-manager</span>=<span class="value">"transactionManager"</span> /&gt;</span>
</code></pre><br>
2. Объявили SessionFactory для Hibernate.<br>
<br>
<pre><code class="xml">
<span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"sessionFactory"</span> <span class="attribute">class</span>=<span class="value">"org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean"</span>
     <span class="attribute">p:dataSource-ref</span>=<span class="value">"dataSource"</span>
     <span class="attribute">p:configLocation</span>=<span class="value">"${hibernate.config}"</span>
     <span class="attribute">p:packagesToScan</span>=<span class="value">"org.krams.tutorial"</span>/&gt;</span>
</code></pre><br>
SessionFactory — фабрика, генерирующая нам объяекты-сессии. Это похоже на Автомобильный завод, работа которого заключается в производстве автомобилей для людей.<br>
<br>
<h4>Что такое сессия (Session)?</h4><br>
Основная функция Сессии — создание, чтение, удаление объектов классов-сущностей(классов помеченных аннотацией <a href="http://habrahabr.ru/users/entity/" class="user_link">Entity</a>).<br>
<br>
Для SessionFactory требуется источник данных, которым в данном уроке является база данных. <br>
SessionFactory требует наличие файла со специфической конфигурацией Hibernate.<br>
<br>
<b>Hibernate.cfg.xml</b><br>
<br>
<pre><code class="xml"><span class="pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="doctype">&lt;!DOCTYPE hibernate-configuration PUBLIC
  "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
  "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd"&gt;</span>
   
<span class="tag">&lt;<span class="title">hibernate-configuration</span>&gt;</span>
  <span class="tag">&lt;<span class="title">session-factory</span>&gt;</span>
   <span class="comment">&lt;!-- We're using MySQL database so the dialect needs to MySQL as well--&gt;</span>
    <span class="tag">&lt;<span class="title">property</span> <span class="attribute">name</span>=<span class="value">"hibernate.dialect"</span>&gt;</span>org.hibernate.dialect.MySQL5InnoDBDialect<span class="tag">&lt;/<span class="title">property</span>&gt;</span>
    <span class="comment">&lt;!-- Enable this to see the SQL statements in the logs--&gt;</span>
    <span class="tag">&lt;<span class="title">property</span> <span class="attribute">name</span>=<span class="value">"show_sql"</span>&gt;</span>false<span class="tag">&lt;/<span class="title">property</span>&gt;</span>
    <span class="comment">&lt;!-- This will drop our existing database and re-create a new one.
      Existing data will be deleted! --&gt;</span>
    <span class="tag">&lt;<span class="title">property</span> <span class="attribute">name</span>=<span class="value">"hbm2ddl.auto"</span>&gt;</span>create<span class="tag">&lt;/<span class="title">property</span>&gt;</span>
  <span class="tag">&lt;/<span class="title">session-factory</span>&gt;</span>
<span class="tag">&lt;/<span class="title">hibernate-configuration</span>&gt;</span>
</code></pre><br>
Здесь мы указали тип нашей базы данных. Мы используем MySQL dialect. Мы используем особенный диалект — MySQL5InnoDBDialect, потому что мы используем потому что мы используем InnoDB движок для управления памятью.<br>
<br>
<h4>Что такое MySQL InnoDB?</h4><br>
 InnoDB — MySQL движок, обеспечивающий безопасность транзакций, позволяющий комиты, откат и восстановление для защиты пользовательских данных.<br>
<br>
Так вот, вернемся обратно к бину SessionFactory, ему необходимо знать, где расположены наши объекты-сущности. Поэтому мы указываем в этом уроке, что они расположены в пакете org.krams.tutorial.<br>
<br>
3. Укажем в файле hibernate-context.xml источники данных.<br>
<br>
<pre><code class="xml"><span class="tag">&lt;<span class="title">bean</span> <span class="attribute">id</span>=<span class="value">"dataSource"</span> <span class="attribute">class</span>=<span class="value">"com.mchange.v2.c3p0.ComboPooledDataSource"</span>
 <span class="attribute">destroy-method</span>=<span class="value">"close"</span>
 <span class="attribute">p:driverClass</span>=<span class="value">"${app.jdbc.driverClassName}"</span>
 <span class="attribute">p:jdbcUrl</span>=<span class="value">"${app.jdbc.url}"</span>
 <span class="attribute">p:user</span>=<span class="value">"${app.jdbc.username}"</span>
 <span class="attribute">p:password</span>=<span class="value">"${app.jdbc.password}"</span>
 <span class="attribute">p:acquireIncrement</span>=<span class="value">"5"</span>
 <span class="attribute">p:idleConnectionTestPeriod</span>=<span class="value">"60"</span>
 <span class="attribute">p:maxPoolSize</span>=<span class="value">"100"</span>
 <span class="attribute">p:maxStatements</span>=<span class="value">"50"</span>
 <span class="attribute">p:minPoolSize</span>=<span class="value">"10"</span> /&gt;</span>
</code></pre><br>
Для эффективного доступа к нашей базе данных мы используем C3p0 пул. Зачем нам необходимо оборачивать наши данные в пул?<br>
<br>
Соединения JDBC чаще управляются через пул, нежели напрямую через драйвер. Примеры пулов соединений BoneCP, C3P0 и DBCP.<br>
<br>
<h4>Зачем нужен пул?</h4><br>
 При разработке программного пул соединений является кешем для открытых соединений. Пулы необходимы для повышения производительности. Открытие и проведение соединения с базой, каждым пользователем, требуют много ресурсов. А при использовании пулов, требуется только извлечь нужное соединение из кеша, а не снова устанавливать соединение.<br>
<br>
Особенности подкдючения к базе данных мы указываем в файле spring.properties.<br>
<br>
<b>spring.properties</b><br>
<br>
<pre><code class="java"># database properties
app.jdbc.driverClassName=com.mysql.jdbc.Driver
app.jdbc.url=jdbc:mysql:<span class="comment">//localhost/mydatabase</span>
app.jdbc.username=root
app.jdbc.password=
 
#hibernate properties
hibernate.config=/WEB-INF/hibernate.cfg.xml
</code></pre><br>
Имя моей базы данных — mydatabase. <br>
<br>
Как альтернатива мы можем указать эти особенности прям в файле hibernate-context.xml.<br>
<br>
Преимущество использования раздельных файлов—конфигураций, в инкапсуляции данных касающихся соединения с базой данных.<br>
<br>
Мы завершили наше приложение. Нам удалось создать простое Spring MVC приложение с использованием Hibernate, для доступа к MySQL.<br>
<br>
Для доступа к главной странице введите следующий URL: <a href="http://localhost">localhost</a>:8080/spring-hibernate-mysql/krams/main/persons<br>
<br>
Лучший способ изучения — попытаться создать приложение самому.<br>
<br>
Для скачивания проекта перейдите по ссылке: <a href="http://spring-mvc-hibernate-annotations-integration-tutorial.googlecode.com/files/spring-hibernate-mysql.zip">spring-mvc-hibernate-annotations-integration-tutorial.googlecode.com/files/spring-hibernate-mysql.zip</a><br>
<br>
<b>UPD:</b> Как и обещал — ссылка на github: <a href="https://github.com/sa4ek/spring-hibernate-mysql/tree/master">github.com/sa4ek/spring-hibernate-mysql/tree/master</a>
            <div class="clear"></div>
        </div>
