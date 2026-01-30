### model

using System;

using System.ComponentModel.DataAnnotations;

using System.Diagnostics.CodeAnalysis;
 
namespace CompanyService.WebAPI.Models

{

    public class Company

    {

        [Required]

        [Company]

        [StringLength(35,MinimumLength =5 ,ErrorMessage = "CompanyName is invalid: CompanyName must contain a minimum of 5 characters and a maximum of 35, and it must start with 'Company Name:'")]

        public string CompanyName { get; set; }

        [Required]

        [Employee]

        public int NumberOfEmployees { get; set; }

        [Required]

        [Salary]

        public int AverageSalary { get; set; }

    }

    public class CompanyAttribute : ValidationAttribute

    {

        protected override ValidationResult IsValid(object value, ValidationContext validationContext)

        {

            if(value == null || string.IsNullOrWhiteSpace(value.ToString()))

            {

                return new ValidationResult("CompanyName is invalid: CompanyName must contain a minimum of 5 characters and a maximum of 35, and it must start with 'Company Name:'");
 
            }

            string s = value.ToString();

            if(!s.StartsWith("Company Name"))

            {

                return new ValidationResult("CompanyName is invalid: CompanyName must contain a minimum of 5 characters and a maximum of 35, and it must start with 'Company Name:'");

            }

            return ValidationResult.Success;

        }

    }

    public class EmployeeAttribute : ValidationAttribute

    {

        protected override ValidationResult IsValid(object value, ValidationContext validationContext)

        {

            if(value == null)

            {

                return new ValidationResult("NumberOfEmployees is invalid: NumberOfEmployees must be greater than 1");

            }

            int a = (int)value;

            if (a <= 1)

            {

                return new ValidationResult("NumberOfEmployees is invalid: NumberOfEmployees must be greater than 1");

            }

            return ValidationResult.Success;

        }

    }

    public class SalaryAttribute : ValidationAttribute

    {

        protected override ValidationResult IsValid(object value, ValidationContext validationContext)

        {

            if(value == null)

            {

                return new ValidationResult("AverageSalary is invalid: AverageSalary must be greater than 0");

            }

            int a = (int)value;

            if(a <= 0)

            {

                return new ValidationResult("AverageSalary is invalid: AverageSalary must be greater than 0");

            }

            return ValidationResult.Success;

        }

    }

}
 