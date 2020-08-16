#######
Примеры
#######

Поле объекта
------------

.. code-block:: java
	:linenos:

	KeroSecurityManager manager = KeroSecurityManager.getInstance();

	manager
	    .type(TestObject.class)
	        .defaultDeny()
	        .property("text")
	            .grantFor("OWNER");

	TestObject obj = new TestObject();

	TestObject protectedWithOwner = manager.protect(obj, "OWNER");
	TestObject protectedWithSomeRole = manager.protect(obj, "SOME_ROLE");

	protectedWithOwner.getText(); // <- OK
	protectedWithSomeRole.getText(); // <- throw AccessException

С использованием аннотаций:

.. code-block:: java
	:linenos:

	@DefaultDeny
	class TestObject {

	    @GrantFor("OWNER")
	    private String text;

	    public String getText() {

	        return this.text;
	    }
	}

.. warning::
	Защита поля - защита его get метода! Не используйте прямой доступ к полям.

Интерцептор (таргетированный)
-----------------------------

.. code-block:: java
	:linenos:

	KeroSecurityManager manager = KeroSecurityManager.getInstance();

	manager
	    .type(TestObject.class)
	        .defaultDeny()
	        .property("text")
	            .grantFor("OWNER")
	            .addDenyInterceptor((obj)-> {

	            	return "Friends not have access!";
	            }, "FRIEND");

	TestObject obj = new TestObject();

	TestObject protectedWithOwner = manager.protect(obj, "OWNER");
	TestObject protectedWithFriend = manager.protect(obj, "FRIEND");
	TestObject protectedWithSomeRole = manager.protect(obj, "SOME_ROLE");

	protectedWithOwner.getText(); // <- return this.text;
	protectedWithFriend.getText(); // <- return "Friends not have access!";
	protectedWithSomeRole.getText(); // <- throw AccessException

С использованием аннотаций:

.. code-block:: java
	:linenos:

	@DefaultDeny
	class TestObject {

	    @GrantFor("OWNER")
	    @AddDenyInterceptor(value = TestInterceptor.class, roles = {"FRIEND"})
	    private String text;

	    public String getText() {

	        return this.text;
	    }
	}

	class TestInterceptor extends DenyInterceptorBase {

	    @Override
	    public Object intercept(Object obj) {

	        return "Friends not have access!";
	    }
	}

Интерцептор (дефолтный)
-----------------------------

.. code-block:: java
	:linenos:

	KeroSecurityManager manager = KeroSecurityManager.getInstance();

	manager
	    .type(TestObject.class)
	        .defaultDeny()
	        .property("text")
	            .grantFor("OWNER")
	            .defaultInterceptor((obj)-> {

	            	return "You not have access!";
	            });

	TestObject obj = new TestObject();

	TestObject protectedWithOwner = manager.protect(obj, "OWNER");
	TestObject protectedWithFriend = manager.protect(obj, "FRIEND");
	TestObject protectedWithSomeRole = manager.protect(obj, "SOME_ROLE");

	protectedWithOwner.getText(); // <- return this.text;
	protectedWithFriend.getText(); // <- return "You not have access!";
	protectedWithSomeRole.getText(); // <- return "You not have access!";

С использованием аннотаций:

.. code-block:: java
	:linenos:

	@DefaultDeny
	class TestObject {

	    @GrantFor("OWNER")
	    @DefaultInterceptor(TestInterceptor.class)
	    private String text;

	    public String getText() {

	        return this.text;
	    }
	}

	class TestInterceptor extends DenyInterceptorBase {

	    @Override
	    public Object intercept(Object obj) {

	        return "You not have access!";
	    }
	}

.. seealso::
	Приоритеты интерцепторов
