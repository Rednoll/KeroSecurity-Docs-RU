######################################
Kero-Security + Spring + GraphQL (WIP)
######################################

(в процессе написания)

Здесь мы посмотрим на простой пример использования Kero-Security + Spring + GraphQL.

Задача
======

Есть сущность Человек c некоторым набором свойств. Необходимо предоставить к ней доступ посредством GraphQL с возможностью предоставлять разные уровни доступа:

* OWNER - имеет доступ ко всем своим данным.
* FRIEND - имеет доступ к почте, телефону, дате рождения и списку друзей.
* AUTHED - имеет доступ к id.

Также данные о имени и поле должны быть доступны всем.

Общий план
==========

Для начала опишем схему доступа к сущности, для наглядности как класса так и правил доступа, будем использовать аннотации (вы можете использовать любой способ :doc:`декларации схемы доступа</usage/sources>`).

Далее ...

Напишем QueryResolver

Описание схемы доступа
======================

Опишем схему доступа к сущности с помощью аннотаций:

.. code-block:: java
	:linenos:

	@DefaultDeny
	public class Human {

	  @DefaultGrant
	  private String name;

	  @DefaultGrant
	  private Sex sex;

	  @GrantFor({"OWNER", "FRIEND"})
	  private String mail;
	  
	  @GrantFor({"OWNER", "FRIEND"})
	  private String phone;

	  @GrantFor({"OWNER", "FRIEND"})
	  private LocalDate birthDate;

	  @GrantFor("AUTHED")
	  private Long id;

	  @GrantFor({"OWNER", "FRIEND"})
	  @PropagateRoles({
	    @PropagateRole(from = "OWNER", to = "FRIEND"),
	    @PropagateRole(from = "FRIEND", to = "AUTHED")
	  })
	  private Set<Human> friends;
	}