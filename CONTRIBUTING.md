<?php
session_start();
error_reporting(0);
include('includes/config.php');
if(strlen($_SESSION['alogin'])==0)
    {   
header('location:index.php');
}
else{ 
    ?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
    <meta name="description" content="" />
    <meta name="author" content="" />
    <title>Online Library Management System | Manage Issued Books</title>
    <!-- BOOTSTRAP CORE STYLE  -->
    <link href="assets/css/bootstrap.css" rel="stylesheet" />
    <!-- FONT AWESOME STYLE  -->
    <link href="assets/css/font-awesome.css" rel="stylesheet" />
    <!-- DATATABLE STYLE  -->
    <link href="assets/js/dataTables/dataTables.bootstrap.css" rel="stylesheet" />
    <!-- CUSTOM STYLE  -->
    <link href="assets/css/style.css" rel="stylesheet" />
    <!-- GOOGLE FONT -->
    <link href='http://fonts.googleapis.com/css?family=Open+Sans' rel='stylesheet' type='text/css' />

</head>
<body>
      <!------MENU SECTION START-->
<?php include('includes/header.php');?>
<!-- MENU SECTION END-->
    <div class="content-wrapper">
         <div class="container">
        <div class="row pad-botm">
            <div class="col-md-12">
                <h4 class="header-line">Manage Issued Books</h4>
    </div>
     <div class="row">
    <?php if($_SESSION['error']!="")
    {?>
<div class="col-md-6">
<div class="alert alert-danger" >
 <strong>Error :</strong> 
 <?php echo htmlentities($_SESSION['error']);?>
<?php echo htmlentities($_SESSION['error']="");?>
</div>
</div>
<?php } ?>
<?php if($_SESSION['msg']!="")
{?>
<div class="col-md-6">
<div class="alert alert-success" >
 <strong>Success :</strong> 
 <?php echo htmlentities($_SESSION['msg']);?>
<?php echo htmlentities($_SESSION['msg']="");?>
</div>
</div>
<?php } ?>



   <?php if($_SESSION['delmsg']!="")
    {?>
<div class="col-md-6">
<div class="alert alert-success" >
 <strong>Success :</strong> 
 <?php echo htmlentities($_SESSION['delmsg']);?>
<?php echo htmlentities($_SESSION['delmsg']="");?>
</div>
</div>
<?php } ?>

</div>


        </div>
 

<form method="post" action="">
    <label>Select Department:</label>
    <select name="Department">
<option value="">Select Department</option>
        <option value="ALL">ALL</option>
        <option value="CSE">CSE</option>
        <option value="ECE">ECE</option>
        <option value="EEE">EEE</option>
        <option value="MECH">MECH</option>
        <option value="CIVIL">CIVIL</option>
        
        <!-- Add more branch options as needed -->
    </select>

    <input type="submit" name="submit" value="Filter">
</form>









           <div class="row">
                <div class="col-md-12">
                    <!-- Advanced Tables -->
                    <div class="panel panel-default">
                        <div class="panel-heading">
                          Issued Books 
                        </div>
                        <div class="panel-body">
                            <div class="table-responsive">
                                <table class="table table-striped table-bordered table-hover" id="dataTables-example">
                                    <thead>
                                        <tr>
                                            <th>#</th>
                                            <th>Faculty Id</th>
                                            <th>Faculty Name</th>
                                            <th>Faculty Branch</th>
                                            <th>Book Name</th>
                                            <th>Book Image</th>
                                            <th>ISBN </th>
                                            <th>Issued Date</th>
                                          <th>Return Date</th>
                                            <th>Action</th>
                                        </tr>
                                    </thead>
                                    <tbody>



<?php

    if (isset($_POST['submit'])) {
        $branchFilter = $_POST['Department'];

        // Check if "All" is selected for both branch and semester filters
        if ($branchFilter == "ALL") {
            // Display all students, no need for specific filters
            $sql = "SELECT faculty.username, faculty.FacultyName, faculty.Department, faculty.FacultyEmail, tblbooks.BookName, tblbooks.bookImage, tblbooks.ISBNNumber, tblissuedbookdetailsfaculty.IssuesDate, tblissuedbookdetailsfaculty.RenewesDate, tblissuedbookdetailsfaculty.ReturnDate, tblissuedbookdetailsfaculty.id as rid FROM tblissuedbookdetailsfaculty JOIN faculty ON faculty.username = tblissuedbookdetailsfaculty.FacultyID JOIN tblbooks ON tblbooks.id = tblissuedbookdetailsfaculty.BookId";
        } else {
            // Filter based on selected branch and semester
            $sql = "SELECT faculty.username, faculty.FacultyName, faculty.Department, faculty.FacultyEmail, tblbooks.BookName, tblbooks.bookImage, tblbooks.ISBNNumber, tblissuedbookdetailsfaculty.IssuesDate, tblissuedbookdetailsfaculty.RenewesDate, tblissuedbookdetailsfaculty.ReturnDate, tblissuedbookdetailsfaculty.id as rid FROM tblissuedbookdetailsfaculty JOIN faculty ON faculty.username = tblissuedbookdetailsfaculty.FacultyID JOIN tblbooks ON tblbooks.id = tblissuedbookdetailsfaculty.BookId WHERE faculty.Department = :branchFilter ";
        }

        $query = $dbh->prepare($sql);
        
        // Bind parameters if filters are not "All"
        if ($branchFilter != "ALL" && $semesterFilter != "ALL") {
            $query->bindParam(':branchFilter', $branchFilter, PDO::PARAM_STR);
            $query->bindParam(':semesterFilter', $semesterFilter, PDO::PARAM_STR);
        }
    }
 else
 {
        // No filters selected, show all records
        $sql = "SELECT faculty.username, faculty.FacultyName, faculty.Department, faculty.FacultyEmail, tblbooks.BookName, tblbooks.bookImage, tblbooks.ISBNNumber, tblissuedbookdetailsfaculty.IssuesDate, tblissuedbookdetailsfaculty.RenewesDate, tblissuedbookdetailsfaculty.ReturnDate, tblissuedbookdetailsfaculty.id as rid FROM tblissuedbookdetailsfaculty JOIN faculty ON faculty.username = tblissuedbookdetailsfaculty.FacultyID JOIN tblbooks ON tblbooks.id = tblissuedbookdetailsfaculty.BookId";
        $query = $dbh->prepare($sql);
    }

    $query->execute();
    $results = $query->fetchAll(PDO::FETCH_OBJ);
    $cnt = 1;
if($query->rowCount() > 0)
{
foreach($results as $result)
{               ?>                                      
                                        <tr class="odd gradeX">
                                            <td class="center"><?php echo htmlentities($cnt);?></td>
                                            <td class="center"><?php echo htmlentities($result->username);?></td>

                                            <td class="center"><?php echo htmlentities($result->FacultyName);?></td>

                                            <td class="center"><?php echo htmlentities($result->Department);?></td>

                                            <td class="center"><?php echo htmlentities($result->BookName);?></td>
                                            <td class="center" width="100">
<img src="bookimg/<?php echo htmlentities($result->bookImage);?>" width="50">

                                            <td class="center"><?php echo htmlentities($result->ISBNNumber);?></td>
                                            <td class="center"><?php echo htmlentities($result->IssuesDate);?></td>
                                            <td class="center"><?php if($result->ReturnDate=="")
                                            {
                                                echo htmlentities("Not Return Yet");
                                            } else {


                                            echo htmlentities($result->ReturnDate);
}
                                            ?></td>
                                            <td class="center">

                                            <a href="update-issue-bookdeails-of-faculty.php?rid=<?php echo htmlentities($result->rid);?>"><button class="btn btn-primary"><i class="fa fa-edit "></i> Edit</button> 
                                         
                                            </td>
                                        </tr>
 <?php $cnt=$cnt+1;}} ?>                                      
                                    </tbody>
                                </table>
                            </div>
                            
                        </div>
                    </div>
                    <!--End Advanced Tables -->
                </div>
            </div>


            
    </div>
    </div>

     <!-- CONTENT-WRAPPER SECTION END-->
  <?php include('includes/footer.php');?>
      <!-- FOOTER SECTION END-->
    <!-- JAVASCRIPT FILES PLACED AT THE BOTTOM TO REDUCE THE LOADING TIME  -->
    <!-- CORE JQUERY  -->
    <script src="assets/js/jquery-1.10.2.js"></script>
    <!-- BOOTSTRAP SCRIPTS  -->
    <script src="assets/js/bootstrap.js"></script>
    <!-- DATATABLE SCRIPTS  -->
    <script src="assets/js/dataTables/jquery.dataTables.js"></script>
    <script src="assets/js/dataTables/dataTables.bootstrap.js"></script>
      <!-- CUSTOM SCRIPTS  -->
    <script src="assets/js/custom.js"></script>
</body>
</html>
<?php } ?>
