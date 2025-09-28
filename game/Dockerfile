# Use nginx alpine for smaller image size
FROM nginx:alpine

# Copy the game files to nginx html directory
COPY . /usr/share/nginx/html/

# Expose port 80
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]