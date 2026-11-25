@startuml
skinparam style strictuml
left to right direction

' Déclaration des acteurs
actor "Utilisateur de la console" as user

rectangle "BT Bank" {
  ' Cas d'utilisation
  usecase "Consulter la liste des clients" as UC_Consult
  usecase "Consulter les comptes d'un client" as UC_Counts
  usecase "Effectuer un dépôt" as UC_Deposit
  usecase "Effectuer un retrait" as UC_WithDraw
  usecase "Consulter historique" as UC_Histo

  ' Notes rattachées aux cas d'utilisation
  'note right of UC_vue
  'end note

  
  user -- UC_Consult
  user -- UC_Counts
  user -- UC_Deposit
  user -- UC_WithDraw
  user -- UC_Histo
}
@enduml