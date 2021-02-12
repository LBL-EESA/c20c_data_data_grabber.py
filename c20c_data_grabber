#!/usr/bin/env python3
"""Extracts data from a given C20C+ run"""
import subprocess
import os


def extract_c20c_data(
        experiment = "All-Hist",
        run = "run001",
        variable_list = None,
        institution = "LBNL",
        model = "CAM5-1-1degree",
        path_template = "/nersc/s/stoned/C20C/{institution}/{model}/{experiment}/est1/v2-0/3hr/atmos/{variable}/{run}/{variable}_A3hr_{model}_{experiment}_est1_v2-0_{run}.tar",
        verbose = True,
        output_directory = None,
        verify_first = True,
        ):
    """
        Extracts variables from the C20C+ archive on NERSC HPSS.

        input:
        ------

            experiment      : the experiment from which to extract data

            run             : the run identifier (e.g., run001)

            variable_list   : a list of variable names
                              (defaults to variable_list = ["hus", "ua", "va"])

            institution     : the institution label

            model           : the model name

            path_template   : a template for determining the paths
                              to the input variables.

            verbose      : flags whether to be verbose

            output_directory: the directory to which to write the files
                              (defaults to the same directory as the htar file)

            verify_first    : flags whether to verify that all the requested
                              tar files exist in the expected locations.

        output:
        -------

            Run `htar` to extract data from the corresponding tar files on disk.


        It is assumed that all variables listed are at the same time frequency
        (3hr) and in the same variable group (e.g., atmos).

    """

    # define the verbose print function
    def vprint(*args):
        """ Prints only if verbose is True. """
        if verbose:
            print(*args)


    # set the default variable list
    if variable_list is None:
        variable_list = ["hus", "ua", "va"]


    # construct paths and verify that files exist
    if verify_first:
        vprint("Checking that expected tar files exist on tape...")
    htar_paths = {}
    for variable in variable_list:
        htar_path = path_template.format(
                experiment = experiment,
                run = run,
                variable = variable,
                institution = institution,
                model = model)
        htar_paths[variable] = htar_path

        if verify_first:
            try:
                hsi_output = subprocess.run(
                        ["hsi",f"ls {htar_path}".rstrip()],
                        check = True,
                        capture_output = True,
                        )
            except:
                raise RuntimeError(f"Failed to verify that `{htar_path}` exists.")

    # extract variables from tape
    for variable in variable_list:
        htar_path = htar_paths[variable]

        # determine the output directory
        if output_directory is None:
            # construct the output directory such that it mirrors that of C20C
            i_path_start = htar_path.find(institution)
            path_substr = htar_path[i_path_start:]
            out_dir = os.path.dirname(path_substr)
        else:
            out_dir = output_directory

        # make sure that the output directory exists
        os.makedirs(out_dir, exist_ok = True)

        # save the current directory
        cwd = os.getcwd()

        # move to the output directory
        os.chdir(out_dir)

        vprint(f"Extracting `{variable}` from `{htar_path}`; this may take some time...")
        htar_output = subprocess.run(
                ["htar","-xf",f"{htar_path}"],
                check = True,
                capture_output = False,
                )
           
        # move back to the base directory
        os.chdir(cwd)





if __name__ == "__main__":
    import argparse

    # define the input arguments
    parser = argparse.ArgumentParser()
    parser.add_argument(
            "experiment",
            help = "the experiment from which to extract data (e.g., All-Hist)"
            )
    parser.add_argument(
            "run",
            help = "the run identifier (e.g., run001)"
            )
    parser.add_argument(
            "institution",
            help = "the institution label"
            )
    parser.add_argument(
            "model",
            help = "the model name (e.g., CAM5-1-1degree"
            )
    parser.add_argument(
            "--variable_list",
            help = "a list of variable names",
            type = lambda arg: arg.split(','),
            default = None,
            )
    parser.add_argument(
            "--path_template",
            help = "a template for determining the paths to the input variables.",
            default = "/nersc/s/stoned/C20C/{institution}/{model}/{experiment}/est1/v2-0/3hr/atmos/{variable}/{run}/{variable}_A3hr_{model}_{experiment}_est1_v2-0_{run}.tar"
            )
    parser.add_argument(
            "--output_directory",
            help = "the directory to which to write the files (defaults to mirroring the C20C directory structure in the current directory)",
            default = None,
            )
    parser.add_argument(
            "-q", "--quiet",
            help = "Flags that diagnostic output should not be printed.",
            default = False,
            action = "store_true",
            )
    parser.add_argument(
            "--no_verify_first",
            help = "Flags that tarfile presence shouldn't be verified on HPSS prior to running htar.",
            default = False,
            action = "store_true",
            )

    # parse the input arguments
    args = parser.parse_args()

    extract_c20c_data(
        experiment = args.experiment,
        run = args.run,
        variable_list = args.variable_list,
        institution = args.institution,
        model = args.model,
        path_template = args.path_template,
        verbose = not args.quiet,
        output_directory = args.output_directory,
        verify_first = not args.no_verify_first,
    )

