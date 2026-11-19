# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

This is a bad idea because anyone could access the AWS credentials and use them to access my resources within AWS without my knowledge. In addition the credentials will eventually expire and become invalid for the Docker container. When that happens, we will have to update the image with the new AWS credentials every time the Learner Lab ends.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The function first checked for MONGODB_ATLAS_URL and the other two variables in os.environ, but could not find them. It then used my AWS credentials from inside the container and connected to the system using boto3.client(). Finally, it used the get_parameter() to retrieve the JSON object with my Atlas variables and connected to them by converting the variables into a Python dictionary through json.loads().

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: df754ffeb6e5 
After: 9082fe2271fa 
It is a problem because the smallest change within the Dockerfile changes the ID of the image even though it is the same image. It has the same name but a different ID, so if two people download the image, they could be using different ones and make the file run differently. Instead, I should have assigned the updated image with a new ID, so that it is clear that there is a difference or update.
 
