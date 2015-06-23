.. _java_programm_einbettung:

SQL in ein JAVA Programm einbetten
==================================

Für den Aufgabenteil 4 wird der entsprechende Treiber benötigt:

`Download Treiber
<https://github.com/Glur4k/SQLTutorium/raw/master/ojdbc6.jar>`_.

Dieser muss in Netbeans / Eclipse als externe Library eingebunden werden.

Anhand von Beispielen soll nun gezeigt werden, wie man die Verbindung herstellt und SQL-Statements ausführt werden.


.. note::

  Es ist sehr empfehlenswert die SQL-Statements zuerst im SQL Developer auszuführen und zu testen, das kann viel Fehlersucherei vermeiden!


.. _Insert-Statement:

Insert-Statement
^^^^^^^^^^^^^^^^

Das erste Beispiel zeigt, wie man Daten in die Tabelle einfügt.
Wichtig beim ausführen ist hierbei, dass man mit dem VPN verbunden ist. Außerdem müssen natürlich Benutzername und Passwort richtig ersetzt werden.

.. code:: java

  import java.sql.*;

  public class Template {

      public static void main(String args[]) {

          String name = "dbsysXX";
          String passwd = "passwort";
          Connection conn = null;
          Statement stmt = null;

          try {
              // Treiber laden
              DriverManager.registerDriver(new oracle.jdbc.OracleDriver());

              // String für DB-Connection
              String url = "jdbc:oracle:thin:@oracle12c.in.htwg-konstanz.de:1521:ora12c";

              // Verbindung erstellen
              conn = DriverManager.getConnection(url, name, passwd);

              // Transaction Isolations-Level setzen
              conn.setTransactionIsolation(conn.TRANSACTION_SERIALIZABLE);

              conn.setAutoCommit(false);
              stmt = conn.createStatement();

              // INSERT-Query
              StringBuilder sb = new StringBuilder();
              sb.append("INSERT INTO ferienwohnung VALUES ('Name', 'Deutschland', 2)");

              // Query ausführen - einfügen
              String myInsertQuery = sb.toString();
              stmt.executeUpdate(myInsertQuery);

              stmt.close();
              conn.commit();
              conn.close();
          } catch (SQLException se) {
              System.out.println("SQL Exception occurred while establishing connection to DBS: \n"
                      + se.getMessage());
              try {
                  conn.rollback();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
              System.exit(-1);
          }
      }
  }


.. _Select-Statement:

Select-Statement
^^^^^^^^^^^^^^^^

Das zweite Beispiel zeigt, wie man ein Select-Statement ausführt.
Hierbei wird angenommen, dass es die Spalten FName, Landname und Anz_Zimmer gibt.

.. code:: java

  import java.sql.*;

  public class Template {

      public static void main(String args[]) {

          String name = "dbsysXX";
          String passwd = "passwort";
          Connection conn = null;
          Statement stmt = null;
          ResultSet rset = null;

          try {
              // Treiber laden
              DriverManager.registerDriver(new oracle.jdbc.OracleDriver());

              // String für DB-Connection
              String url = "jdbc:oracle:thin:@oracle12c.in.htwg-konstanz.de:1521:ora12c";

              // Verbindung erstellen
              conn = DriverManager.getConnection(url, name, passwd);

              // Transaction Isolations-Level setzen
              conn.setTransactionIsolation(conn.TRANSACTION_SERIALIZABLE);

              conn.setAutoCommit(false);
              stmt = conn.createStatement();

              // SELECT-Query
              StringBuilder sb = new StringBuilder();
              sb.append("SELECT * FROM ferienwohnung");

              String mySelectQuery = sb.toString();
              rset = stmt.executeQuery(mySelectQuery);

              // "Tabellen"-Ansicht bauen
              System.out.printf("Name     |      ");
              System.out.printf("Land     |  Zimmer\n");
              System.out.println("----------------------------------");

              while (rset.next()) {
                  System.out.printf(rset.getString("Fname") + " | ");
                  System.out.printf(rset.getString("Landname") + " | "
                          + rset.getInt("Anz_Zimmer") + " | ");
                  System.out.println("");
              }

              stmt.close();
              conn.commit();
              conn.close();
          } catch (SQLException se) {
              System.out.println("SQL Exception occurred while establishing connection to DBS: \n"
                      + se.getMessage());
              try {
                  conn.rollback();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
              System.exit(-1);
          }
      }
  }
